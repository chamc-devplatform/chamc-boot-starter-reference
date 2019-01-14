#JPA
###1 介绍
> 本文档摘要重构了[JPA官方文档](https://docs.spring.io/spring-data/jpa/docs/1.11.6.RELEASE/reference/html/ "JPA官方文档")的部分内容，引用了部分代码

- Spring Data JPA是Spring基于Hibernate开发的一个对象关系映射JPA框架。
- Spring Data repository抽象的目标是显著减少为各种持久性框架实现数据访问层的样板代码。

###2 基础知识
- 本章节简要介绍Spring Data JPA的核心接口、技术
- 如果你希望尽快上手使用可选择跳过次章节

####2.1 核心概念

- **Repository**：最顶层的接口，是一个空的接口，目的是为了统一所有`Repository`的类型，它将域类和域类的id类型作为类型参数进行管理，且能让组件扫描的时候自动识别。
- **CrudRepository** ：是`Repository`的子接口，为其管理的实体类提供CRUD的功能
- **PagingAndSortingRepository**：是`CrudRepository`的子接口，添加分页和排序的功能
- **JpaRepository**：是`PagingAndSortingRepository`的子接口，增加了一些实用的功能，比如：批量操作等

####2.2 实现思路

标准的CRUD功能存储库实现的查询比较底层，用Spring Data，定义这些查询变成了以下四步:

1. 定义一个继承Repository接口或者它的子接口的接口,并且设定泛型为其所管理的类类型和类的ID类型，对于开发平台来讲，即为BaseRepository
	- 平台提供`BaseRepository`：	
	
			public interface BaseRepository<T> extends JpaRepository<T, Long> {...}

	- 用户自定义接口继承`BaseRepository`:
	
			public interface UserRepository extends BaseRepository<User> {...}

2. 定义查询的方法

		public interface UserRepository extends BaseRepository<User> {
			User findByAccount(String account);
		}

3. 让`Spring`为这些接口创建代理实例

		@EnableJpaRepositories(repositoryBaseClass = BaseRepositoryImpl.class, basePackages = "${chamc.web.scan.repository}")

4. 在`service`中获得`repository`实例并且使用
		
		private @Autowired PersonRepository repository;


###3 基础使用

- 提供两种方式解析用户查询意图:一种是直接通过方法的命名规则去解析,第二种是通过`@Query`注解去解析

####3.1 方法名动态查询

- 方法的命名规则（其中`findBy`可以替换成`readBy`、`queryBy`、`getBy`等） : 
	
		find 【限定条件】 By 【实体属性条件】 【排序条件】

- `Spring Data`提供一个查询构建机制来构建对实体的约束查询,该机制剥离前缀`findBy`，`readBy`，`queryBy`，`countBy`，和 `getBy`后解析其余部分。
- By充当分隔符以指示实际条件的开始，条件可以是由And和Or连接的实体属性条件
- 除了And和Or外，属性表达式还支持 `Between`, `LessThan`, `GreaterThan`, `Like`，具体请见后文
	
【构建查询】	

	public interface StudentRepository extends BaseRepository<Student> {
		
		// 根据Student实体的name属性精确查找
		List<Student> findByName(String name);
		// 按属性条件中出现的属性顺序匹配参数，与参数名无关
		List<Student> findByNameAndBirthday(String a, Date b);
	
		// 需要去重时distinct可写在以下两个位置之一
		List<Student> findDistinctStudentByNameOrNo(String name, String no);
		List<Student> findStudentDistinctByNameOrNo(String name, String no);
	
		// 对特定属性忽略大小写
		List<Student> Name(String name);
		// 对查询条件中可忽略大小写的所有属性做忽略
		List<Student> findByNameAndNoAllIgnoreCase(String name, String no);
	
		// 静态排序(Asc升序，Desc降序，默认升序可省略)
		List<Student> findByNameOrderByNoAsc(String name);
		List<Student> findByNameOrderByNoDesc(String name);
		
	}

【属性表达式】

> - 如前面的示例所示，属性表达式只能引用其管理的实体的直接属性。下面将展示通过遍历嵌套属性来定义约束。
> - 假设学生含有属性`clazzName`（班级名）且关联`Clazz`（班级）如下，假设`Clazz`含有属性`name`

	@ManyToOne(fetch = FetchType.LAZY)
	@JoinColumn(name = "clazz_id_")
	private Clazz clazz;
	
	@Column(name = "clazz_name_")
	private String clazzName;

> - 则查询`Clazz`中的`name`与查询`Student`中的`clazzName`均为方法1的形式，这会造成解析结果与期望不符。
> - 解析算法首先将整个部分（ClazzName）解释为属性，并检查域类中是否具有该名称的属性。如果算法成功，则使用该属性。如果没有，算法将递归的从右侧按驼峰分隔，并尝试找到相应的属性，在我们的示例中，`Clazz`和`Name`。
> - 若要显示地指定遍历的分割点可使用 `-` 符号，如方法2所示

	// 方法1
	List<Student> findByClazzName(String name);
	// 方法2
	List<Student> findByClazz_Name(String name);

【特殊参数】

> - 对于一般参数，JPA按照其在属性表达式中出现的顺序进行匹配。同时，还支持一些特定类型，如`Pageable`和`Sort`来实现动态的分页和排序。
> - **Pageable** : `org.springframework.data.domain.Pageable`
> - **Sort** : `org.springframework.data.domain.Sort`

	Page<Student> findByName(String name, Pageable pageable);
	
	List<Student> findByName(String name, Sort sort);

【限制结果数量】

> - 查询方法的结果可以通过关键字`First`或`Top`来限制，可以互换使用。如果省略数字，则默认结果大小为1。

	Student findFirstByOrderByNameAsc();

	Student findTopByOrderByAgeDesc();

	//此时分页是对限定之后的结果分页
	Page<Student> findFirst10ByName(String lastname, Pageable pageable);

	List<Student> findFirst10ByName(String lastname, Sort sort);

####3.2 使用@Query注解

> - `@Query`注解将JPQL语句绑定到方法，对于某些查询更方便
> - 默认情况下，`Spring Data JPA`将使用基于位置的参数绑定,如下的前两个示例
> - 可以使用`@Param`注释为方法参数指定具体名称并在查询中绑定名称，如第三个示例
> - 对于修改和删除的操作,必须添加`@Modifying`注解,以通知`Spring Data` 这是一个`DELETE`或`UPDATE`操作。`UPDATE`或者`DELETE`操作需要使用事务，此时需要在`Service`层的方法上添加事务操作。

	// ?1 -> 第一个参数
	@Query("SELECT s FROM Student s WHERE s.name = ?1")
	Student findByNameUseQuery(String name);

	// 使用LIKE的示例
	@Query("SELECT s FROM Student s WHERE s.name LIKE %?1")
	List<Student> findByNameEndsWith(String nameSuffix);

	// :name -> 名字为name的参数
	@Query("SELECT s FROM Student s WHERE s.name = :name OR s.no = :no")
	Student findByNameOrNo(@Param("name") String tmpName, @Param("no") String tmpNo);
	
	// UPDATE操作
	@Modifying
	@Query("UPDATE Student s SET s.name = ?1 WHERE s.no = ?2")
	int setNameForNo(String name, String no);
	
	// DELETE操作
	@Modifying
	@Query("delete from Student s where s.id = ?1")
	void deleteIByStuId(long stuId);

####3.3 映射（Projections）

> - 进行数据查询时，有时候并不需要把全部字段查询出来，只要查询部分的字段即可。
> - 那么就需要使用到`Sping-Data-Jpa`中的映射（亦称为投影）功能了。

请看下面示例，假设我们需要查询学生的三个属性,姓名(name)、学号(no)和年龄(age)

	public interface StudentProjection {
		
		String getName();
		String getNo();
		Long getAge();

		// 虚拟属性
		@Value("#{ target.clazz.no }")
		String getClazzNo();
		
		// 虚拟属性，可根据需要不暴露真实值，一般用于密码
		@Value("#{ target.sex == null || target.sex.empty ? null : '*' }")
		String getSex();
	
	}

【说明】

1.投影在实体类型与方法签名之间定义了一个与暴露属性相关的联结。因此定义`getter`方法时必须根据实体属性对应命名。

例如：实体属性为`firstName`，则`getter`方法必须命名为`getFirstName`

2.投影可用于调整暴露的数据模型，可以添加虚拟属性，如上面示例中的后两个`getter`方法。

	List<StudentProjection> findProjectionBy();

JPA方法名查询所支持的关键字请参考 [JPA官方文档——方法名关键字](https://docs.spring.io/spring-data/jpa/docs/1.11.6.RELEASE/reference/html/#jpa.query-methods.query-creation "Spring Data JPA - Reference Documentation")

####3.4 利用@Query注解封装结果集

> - 不常用，可跳过此小节
> - 假设需要如下结果集，可先定义结果类如下，需要有带参构造函数：

	@NoArgsConstructor
	@AllArgsConstructor
	public @Data class StudentExt {
		private String name;
		private Long age;
		private String className;
	}		

> - 利用@Query注解可以构造返回集，示例为使用全参构造器

	@Query("SELECT new chamc.boot.starter.demo.jpalyb.entity.ext.StudentExt(s.name, s.age, s.clazz.name) "
			+ "FROM Student s WHERE s.age = ?1")
	List<StudentExt> findStudentExtByAge(Long age);


####3.5 利用Java8 Optional避免空指针

- 利用Optional可以使一些对于null的的处理更优雅，非必须，可跳过此小节。
- Java 8 新特性 Optional 类是一个可以为null的容器对象。如果值存在则isPresent()方法会返回true，调用get()方法会返回该对象。
- 应避免 `if(stu.isPresent()) { ... } else { ... }` 的应用方式。

【在repository中】

	Optional<Student> findById(Long id);

【在service中】

	public Student testOptional(Long id) {
		Optional<Student> stu = repository.findById(id);
		
		// 存在才输出
		stu.ifPresent(System.out::println);
		
		// 存在则获得姓名大写
		stu.map(Student::getName).map(String::toUpperCase).orElse(null);
		
		//return stu.orElse(null);
		// 存在即返回, 无则由函数来产生
		return stu.orElseGet(() -> new Student());
	}

	// 未查询到则抛错
	public List<String> testOptional2(List<Long> ids) {
		List<String> stuNames = ids.stream().map(repository::findById).map(option -> {
    		return option.orElseThrow(() -> new BussinessException("id不正确")).getName();
    	}).collect(Collectors.toList());
		
		return stuNames;
	}
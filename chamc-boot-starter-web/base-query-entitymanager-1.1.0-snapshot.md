#EntityManager及原生SQL
###简介

- `EntityManager` 是JPA中用于增删改查的接口，即对实体Bean进行操作的辅助类。它的作用是连接内存中的java对象和数据库的数据存储。
- 你可以不用关心`EntityManager`的实现与注入，Hibernate完成了实现相关的工作，开发平台使用@PersistenceContext完成了注入的相关工作。
- 当需要使用原生SQL时，建议通过EntityManager的方式调用。更多关于EntityManager的介绍将在后面展开。

###使用EntityManager调用原生SQL

> - 直接使用@Autowired注解注入EntityManager即可，使用createNativeQuery([原生SQL字符串], [返回实例类型])构建查询。
> - 使用setParameter()方法为查询设置参数，支持根据参数索引和命名两种用法
> - 示例为根据学校名模糊查询学生会会长所在班级的学生，并按学生学号正序排列
	
	private @Autowired EntityManager em;

	@SuppressWarnings("unchecked")
	public List<Student> findBySchool(String name) {
		
		name = "%" + name + "%";

		String sql = "SELECT st.* FROM t_student_ st WHERE st.clazz_id_ IN "
				+ "(SELECT p.clazz_id_ FROM t_student_ AS p,t_school_ AS s WHERE s.name_ LIKE ? AND s.student_id_=p.id_) "
				+ "ORDER BY st.no_ ASC";
		
		// 根据参数索引，?1对应setParameter(1, name)
		List<Student> students = em.createNativeQuery(sql, Student.class).setParameter(1, name).getResultList();

		return students;
	}
	// ... WHERE s.name_ LIKE :name ...
	// 根据参数名，setParameter("name", name)对应:name的参数

###EntityManager核心概念
【持久化上下文(PersistenceContext)】

- 是一个受管的Entity实例的集合。

- 监控并管理处于持久化状态的所有实体，是 JPA提供程序 大部分功能的核心。

- 允许持久化引擎执行自动的脏检查，以检查应用程序修改了那些实体实例，然后提供程序会与数据库同步受持久化上下文监控的实例状态。

- 持久化上下文在应用程序中是透明的，我们需要通过EntityManager间接管理它。

![EntityManager核心概念图](https://i.imgur.com/P8Gw923.png)

【EntityManager和PersistenceUnit】

- `EntityManagerFactory`接口中使用的最为频繁的就是第一个createEntityManager()，它能够创建并返回得到一个`EntityManager`接口的实现。
- 持久化单元(PersistenceUnit)用于配置如何获取`EntityManager`。

【JPA实体状态】

- JPA中的实体对象拥有四种状态：瞬时状态（transient），持久化状态（persistent），游离状态（detached），移除状态（removed）。

	- 瞬时状态： 瞬时状态的实体就是一个普通的java对象，和持久化上下文无关，数据库中也没有数据与之对应。
	
	- 持久化状态：持久化状态的实体在持久化上下文中，处于该状态的实体的更新会同步到数据库中。
	- 游离状态：当事务提交后，处于持久化状态的实体就会变为游离状态，处于游离状态的实体已经不在持久化上下文中，其修改不会直接同步到数据库中。
	
	- 移除状态：当调用EntityManager#remove（）一个实体时，该实体就处于移除状态，也不在持久化上下文中。

- 状态间的迁移方式如下图所示，图中的方法都是EntityManager的成员方法：

![实体状态迁移图](https://i.imgur.com/fyoFzRA.png)
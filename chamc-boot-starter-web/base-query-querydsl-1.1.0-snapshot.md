#QueryDSL
###1 介绍
> 本文档摘要重构了[QueryDSL官方文档](http://www.querydsl.com/static/querydsl/4.1.3/reference/html_single/ "QueryDSL官方文档")的部分内容，引用了部分代码

- `Querydsl`是一个通过流畅式API（fluent API）构建静态查询的框架。
- `Querydsl`的核心原则和目的是保证类型安全和一致性。

###2 基础知识
> - 使用`QueryDSL`创建查询的前提是实例化变量和查询实现
> - 对于学生实体(Student)，`QueryDSL`会自动生成一个名为QStudent的查询类型，在QueryDSL查询语句中代表Student。
> - QStudent有一个默认的静态域实例变量：


	public class QStudent extends EntityPathBase<Student> {
		
		public static final QStudent student = new QStudent("student");
	
		...
	
	}

> - 可自定义变量如下：

	QStudent newStudent = new QStudent("myStudent");

> - 创建查询的两种方式如下，推荐使用queryFectory的方式：

	QStudent student = QStudent.student;
	
	//通过JPAQueryFactory构建，推荐此种方式
	JPAQueryFactory qf = repository.getQueryFactory();
	JPAQuery<Student> query1 = qf.selectFrom(student);
	
	//通过new Querydsl的方式
	AbstractJPAQuery<Student, JPAQuery<Student>> query = repository.createDslQuery();
	JPQLQuery<Student> query2 = query.select(student).from(student);

###3 基本使用
【简单查询示例】

	//查询名为student1的学生
	JPAQuery<Student> query = qf.selectFrom(student).where(student.name.eq("student1"));
	
	//返回结果集结果集
	List<Student> list = query.fetch();
	//返回结果集的第一个元素
	Student tmp = query.fetchOne();

【查询条件的拼接】

> - 当查询条件较为复杂时，可提出为局部布尔表达式变量
> 
> - 需要判断是否拼装条件时，可使用`BooleanBuilder`构造并拼装


	JPAQuery<Student> query = qf.selectFrom(student)
			.where(student.name.eq("student1").and(student.age.gt(15L)));

	//条件复杂时
	BooleanExpression nameEq = student.name.eq("student1");
	BooleanExpression ageGt = student.age.gt(15L);
	
	//构造查询条件
	BooleanBuilder condition = new BooleanBuilder();
	if(true) {
		condition = condition.and(nameEq).and(ageGt);
	}
	
	JPAQuery<Student> query = qf.selectFrom(student).where(condition);

【JOIN和FETCH的使用】

> - `Querydsl`支持的关机字包括`inner join`, `join`, `left join` 和 `right join`
> - FETCH代表抓取策略，使用方法`fetchJoin()`表示。由于实体中关联关系一般将`FetchType`配置为`Lazy`，当业务需要抓取该字段时使用`fetchJoin()`如下所示
> - 举例：`.leftJoin(qStudent.clazz, qClazz)`的含义为关联Clazz并重命名为qClazz，可理解为SQL对应`LEFT JOIN FETCH Clazz AS qClazz ON ...`

	QStudent qStudent = QStudent.student;
	QClazz qClazz = QClazz.clazz;
	QGrade qGrade = QGrade.grade;
	
	JPAQueryFactory qf = repository.getQueryFactory();
	JPAQuery<Student> query = qf.select(qStudent)
			.from(qStudent)
			.leftJoin(qStudent.clazz, qClazz).fetchJoin()
			.leftJoin(qClazz.grade, qGrade).fetchJoin();


【分页和排序】

> - 排序使用`orderBy()`,可使用多个字段排序，其中asc()代表升序，desc()代表降序
> - 分页需要传入Pageable参数，并利用该参数实现分页，常用三种方式（推荐使用第一种方式）：

> - 方式一：（推荐）根据分页参数获取查询结果再封装

	public Page<Student> pageExample(Pageable pageable) {
		QStudent qStudent = QStudent.student;
		
		JPAQueryFactory qf = repository.getQueryFactory();
		JPAQuery<Student> query = qf.select(qStudent)
				.from(qStudent);
				
		Long total = query.fetchCount();
		List<Student> result = query.offset(pageable.getOffset())
				.limit(pageable.getPageSize())
				.orderBy(qStudent.age.asc(), qStudent.no.desc())
				.fetch();
		
		return new PageImpl<>(result, pageable, total);
	}

> - 方式二：使用`BaseRepository`中的`findByQuerydsl`方法

	AbstractJPAQuery<Student, JPAQuery<Student>> q = repository.createDslQuery();
	JPQLQuery<Student> query = q.select(qStudent).from(qStudent)
			.orderBy(qStudent.age.asc(), qStudent.no.desc());
			
	return repository.findByQuerydsl(query, pageable, Student.class);

> - 方式三：使用`PageableExecutionUtils`获取分页结果，实际上findByQuerydsl就是使用的这种方式

	Sort sort = new Sort(Direction.DESC,"age");
	PageRequest pageRequest = new PageRequest(pageable.getPageNumber(), pageable.getPageSize(), sort);
	JPQLQuery<Student> queryResult = repository.getQuerydsl().applyPagination(pageRequest, query);
	Page<Student> result = PageableExecutionUtils.getPage(queryResult.fetch(), pageRequest, () -> query.fetchCount());
	

【子查询】

> - 子查询使用`JPAExpressions`构建，本例可看出需要new一个QueryDSL查询对象stu

	QStudent qStudent = QStudent.student;
	QStudent stu = new QStudent("myStu");
	
	JPAQueryFactory qf = repository.getQueryFactory();
	JPAQuery<Student> query = qf.select(qStudent)
			.from(qStudent)
			.where(qStudent.age.eq(
					JPAExpressions.select(stu.age.max()).from(stu))
			);

【多表动态查询】

> - 当需要多表查询时可使用`Tuple`实现，如下示例为将两列作为结果集返回

	JPAQuery<Tuple> query = qf.select(qStudent.name, qClazz.name)
				.from(qStudent)
				.leftJoin(qStudent.clazz, qClazz).fetchJoin();

	List<Tuple> result = query.fetch();

	//由于Tuple的实现实际上是Object数组,取值操作如下
	for(Tuple t : result) {
		String stuName = t.get(0, String.class);
	}

【利用Stream API映射重新组装返回结果】

> - 利用StreamApi可以方便地将结果集处理并重新封装返回
> - 作为中间处理环节时可这样使用，需要返回前端时应使用DTO

	public List<?> StreamApi() {
		QStudent qStudent = QStudent.student;
		
		JPAQueryFactory qf = repository.getQueryFactory();
		List<?> result = qf.selectFrom(qStudent)
				.select(qStudent.name, qStudent.age)
				.fetch()
				.stream()
				.map(tuple -> {
					Map<String, String> map = new HashMap<>();
					map.put("name", tuple.get(qStudent.name));
					map.put("age", tuple.get(qStudent.age).toString());
	                return map;
				}).collect(Collectors.toList());
		
		return result;
	}
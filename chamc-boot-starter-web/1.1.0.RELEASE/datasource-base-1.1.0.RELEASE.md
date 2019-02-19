# 基本使用

## 配置

### 添加依赖

在`pom.xml`中添加web组件依赖，详见[基础信息配置](./base-config-1.1.0.RELEASE.md)。

### 添加配置文件配置

在配置文件`application.properties`中添加以下配置：

1) 开启多数据源

	chamc.em.multi.enable=true

2) 不使用jta事务（使用jta事务见下节[高级使用](./datasource-advance-1.1.0.RELEASE.md)）

	spring.jta.enabled=false

3) 配置默认数据源

	chamc.em.multi.def.ds.url=jdbc:mysql://127.0.0.1:3306/demo-ds-multi 
	chamc.em.multi.def.ds.username=root
	chamc.em.multi.def.ds.password=123456
	#该数据源的entity所对应的包
	chamc.em.multi.def.em.entity.base-packages=com.chamc.boot.demo.ds.book.entity
 	#该数据源的repository所对应的包
	chamc.em.multi.def.em.repository.base-packages=com.chamc.boot.demo.ds.book.repository
 
4) 配置其他数据源名字，例如将另一个数据源命名为order

	#可配多个，用逗号分隔
	chamc.em.multi.names=order

5) 配置其他数据源，需与上一步配置的数据源名相对应（配几个名字即可配几个数据源），例如

	chamc.em.multi.ems.order.ds.url=jdbc:mysql://127.0.0.1:3306/demo-ds-multi-order
	chamc.em.multi.ems.order.ds.username=root
	chamc.em.multi.ems.order.ds.password=123456
	#其他数据源（本例为order数据源）所对应的entity
	chamc.em.multi.ems.order.em.entity.base-packages=com.chamc.boot.demo.ds.order.entity
	#其他数据源（本例为order数据源）所对应的repository
	chamc.em.multi.ems.order.em.repository.base-packages=com.chamc.boot.demo.ds.order.repository

6) 数据源配置还有很多其他属性可根据需要自行配置

下面介绍更多数据源配置，将以默认数据源为例，其他数据源配置将`chamc.em.multi.def.ds`改为`chamc.em.multi.ems.xxx.ds`

	#驱动类名
	chamc.em.multi.def.ds.driver-class-name=

	#默认catalog（非jta事务有效）
	chamc.em.multi.def.ds.default-catalog=

	#数据源启动时的初始化连接数（非jta事务有效），默认：10
	chamc.em.multi.def.ds.initial-size=

	#最大活动连接数，默认：50
	chamc.em.multi.def.ds.max-active=

	#最大空闲连接数（非jta事务有效），默认：和maxActive一样
	chamc.em.multi.def.ds.max-idle=

	#最小空闲连接数，默认：和initialSize一样
	chamc.em.multi.def.ds.min-idle=

	#是否默认自动提交（非jta事务有效），默认：true
	chamc.em.multi.def.ds.default-auto-commit=

	#是否默认只读（非jta事务有效），默认：false
	chamc.em.multi.def.ds.default-read-only=

	#默认事务隔离级别
	chamc.em.multi.def.ds.default-transaction-isolation=

	#从连接池获取连接的最大等待时间，单位：毫秒，默认：30000（30s）
	chamc.em.multi.def.ds.max-wait=

	#从连接池获取连接后（非jta事务有效），是否验证连接可用性，默认：false
	chamc.em.multi.def.ds.test-on-borrow=

	#与数据库建立连接后（非jta事务有效），是否验证连可用性，默认false
	chamc.em.multi.def.ds.test-on-connect=

	#在连接被还回连接池之前（非jta事务有效），是否验证连接可用性，默认：false
	chamc.em.multi.def.ds.test-on-return=

	#是否验证空闲连接可用性，注：jta事务环境下，该属性永远为true，默认：false
	chamc.em.multi.def.ds.test-while-idle=

	#验证连接可用性的query语句，注：jta事务环境下，必须配置，默认：SELECT 1
	chamc.em.multi.def.ds.validation-query=

	#验证连接可用性语句执行超时时间（非jta事务有效），单位：秒，小于0则表示永不超时，默认：-1
	chamc.em.multi.def.ds.validation-query-timeout=

	#自定义验证连接可用性的类名（非jta事务有效），该类应该实现org.apache.tomcat.jdbc.pool.Validator接口
	chamc.em.multi.def.ds.validator-class-name=

	#执行回收空闲连接和不可用连接方法的时间间隔（非jta事务有效），单位：毫秒，默认60000（60s）
	chamc.em.multi.def.ds.time-between-eviction-runs-millis=

	#连接可以保持空闲状态的最小时间，单位：毫秒，默认：60000（60s）
	chamc.em.multi.def.ds.min-evictable-idle-time-millis=

	#连接参数（非jta事务有效），会被拼接到url上，格式：[propertyName=property;]*
	chamc.em.multi.def.ds.connection-properties=

	#连接池里空闲连接不够时，每次新建连接数（jta事务有效），默认：1 
	chamc.em.multi.def.ds.acquire-increment=

	#如果获取的连接不可用，再次获取前的等待时间（jta事务有效），单位：秒，默认：1
	chamc.em.multi.def.ds.acquisition-interval=

	#PrepareStatement缓存数，0表示不缓存，默认：0（不缓存）
	chamc.em.multi.def.ds.prepared-statement-cache-size=

## 使用

### 如何进行数据库操作

有以下方式进行数据库操作：

1) 直接使用repository，例如

	private @Autowired BookRepository repository;

	public Book findOne(Long id) {
		return repository.findOne(id);
	}

2) 通过@PersistenceContext注入指定的EntityManager，使用EntityManager进行数据库操作，例如

	@PersistenceContext(unitName = "order")
	private EntityManager orderEm;

	public List<Order> findAll() {
		return orderEm.createQuery("from Order", Order.class).getResultList();
	}

3) 注入EntityManagerHolderService，并通过它获取需要的EntityManager，再使用EntityManager进行数据库操作，例如

	private @Autowired EntityManagerHolderService emHolder;

	public Object method() {
		EntityManager orderEm = emHolder.getEntityManager("order"); // 获取order的EntityManager
		EntityManager defEm = emHolder.getDefaultEm(); // 获取默认的EntityManager
		... ...
	}

### 如何进行事务控制

接下来将介绍如何使用事务控制(此处介绍的是非jta事务，使用jta事务见[高级使用](./datasource-advance-1.1.0.RELEASE.md))以及使用事务控制的一些坑。

1) 默认数据源使用事务，只需在方法上加`@Transactional`注解

2) 其他数据源使用事务需要指定事务管理器，在方法上加`@Transactional("xxxTransactionManager")`注解，xxx为数据源名称。若不指定则使用默认事务管理器，对于该方法的事务控制将不起作用。

3) 一般的查询方法都不需要加事务控制，但如果你在你的方法里要获取操作配置了懒加载的属性，则需要事务管理，告诉懒加载器使用哪个EntityManger去加载属性。例如：
	
	// 为了提升效率，如果确认只有查询查询操作，则可以设置readOnly = true
	@Transactional(value = "productTransactionManager", readOnly = true)
	public List<Product> findAll() {
		// 由于配置了懒加载，所以这里查出来的product的supplier属性都还没有值
		List<Product> products = this.repository.findAll();
		
		for(Product product : products) {
			// 如果整个方法没有加事务管理，则这里会报错；
			// 因为懒加载在真正去查数据库的时候会用当前的EntityManager去查，但我们整个应用里有多个EntityManager了，所以会报错
			log.info(product.getSupplier().toString());
		}
		return products;
	}

	// Product类
	@Entity @Table(name = "t_product")
	@EqualsAndHashCode(callSuper = true)
	public @Data class Product extends BaseEntity {
	
		@Id @GeneratedValue(generator = "snowflake")
		@GenericGenerator(name = "snowflake", strategy = "com.chamc.boot.web.support.SnowflakeIdGenerator")
		private Long id;
		private String name;
		private Float price;
		
		@ManyToOne(fetch = FetchType.LAZY)
		private Supplier supplier;
	}

**所以最好按照规范来做，如果我们需要使用关联对象，查询时将其fetch出来**，例如：

	public List<Product> findAllAndFetchSupplier() {
		JPAQueryFactory qf = this.repository.getQueryFactory();
		// 在查询product的时候直接将supplier也fetch出来，所以就不存在lazy的问题，就不需要加事务
		JPAQuery<Product> fetchQuery = qf.select(product).from(product).leftJoin(product.supplier, supplier).fetchJoin();
		return fetchQuery.fetch();
	}

4) 当一个方法操作多个数据源并需要使用事务时，应使用jta事务，详见下一节[高级使用](./datasource-advance-1.1.0.RELEASE.md)，下面将举一反例：

	/**
	 * 该方法操作了三个数据源，但由于没有使用jta事务，所以该方法不需要事务管理；<br/>
	 * 应该在具体的操作某个数据源的方法上进行事务管理，如：CustomerService.save。<br/>
	 * 就算在该方法加了事务也没用，他只对其管理的数据源有效，对其他数据源没有效果。<br/>
	 * 比如该方法加了@Transactional，并指定了事务管理器-customerTransactionManager，则对customer数据源的操作会在同一个事务里面。
	 */
	@Transactional(value = "customerTransactionManager")
	public void order(ComposeModel compose) {
		if(Objects.isNull(compose.getOrder().getId())) {
			this.orderService.save(compose.getOrder());
		}
		if(Objects.isNull(compose.getProduct().getId())) {
			this.productService.save(compose.getProduct());
		}
		if(Objects.isNull(compose.getCustomer().getId())) {
			this.customerService.save(compose.getCustomer());
		}
		// 这里抛异常，只有对customer数据源的操作会回滚，order和product都会插入
		throw new BussinessException("customer插入操作回滚。");
	}

### 事务超时时间

#### 介绍

简单地说，transaction timeout就是`statement Timeout * N（需要执行的statement数量） + @（垃圾回收等其他时间）`。transaction timeout用来限制执行statement的总时长。

Spring提供的transaction timeout配置非常简单，它会记录每个事务的开始时间和消耗时间，当特定的事件发生时就会对消耗时间做校验，当超出timeout值时将抛出异常。

#### 使用

1) 在`@org.springframework.transaction.annotation.Transactional()`注解中配置`timeout`属性，单位秒。例：`@Transactional(timeout = 10)`

2) 在配置文件`application.properties`中配置

	spring.transaction.default-timeout=

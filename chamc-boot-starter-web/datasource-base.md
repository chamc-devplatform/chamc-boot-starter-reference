# 基本使用

1. 配置

	1) 在`pom.xml`中添加web组件依赖，详见[基础信息配置](./base-config.md)。

	2) 在配置文件`application.properties`中添加以下配置：

	- 开启多数据源

			chamc.em.multi.enable=true

	- 不使用jta事务（使用jta事务见下节[高级使用](./datasource-advance.md)）

			spring.jta.enabled=false

	- 配置默认数据源

			chamc.em.multi.def.ds.url=jdbc:mysql://127.0.0.1:3306/demo-ds-multi 
			chamc.em.multi.def.ds.username=root
			chamc.em.multi.def.ds.password=123456
			chamc.em.multi.def.em.entity.base-packages=com.chamc.boot.demo.ds.book.entity  //该数据源的entity所对应的包
			chamc.em.multi.def.em.repository.base-packages=com.chamc.boot.demo.ds.book.repository //该数据源的repository所对应的包
	 
	- 配置其他数据源名字，例如将另一个数据源命名为order

			chamc.em.multi.names=order // 可配多个，已逗号分隔

	- 配置其他数据源，需与上一步配置的数据源名相对应（配几个名字即可配几个数据源），例如

			chamc.em.multi.ems.order.ds.url=jdbc:mysql://127.0.0.1:3306/demo-ds-multi-order
			chamc.em.multi.ems.order.ds.username=root
			chamc.em.multi.ems.order.ds.password=123456
			chamc.em.multi.ems.order.em.entity.base-packages=com.chamc.boot.demo.ds.order.entity //其他数据源（本例为order数据源）所对应的entity
			chamc.em.multi.ems.order.em.repository.base-packages=com.chamc.boot.demo.ds.order.repository //其他数据源（本例为order数据源）所对应的repository

	- 数据源配置还有很多其他属性可根据需要自行配置

2. 使用

	1) 有以下方式进行数据库操作：

	- 直接使用repository，例如

			private @Autowired BookRepository repository;

			public Book findOne(Long id) {
				return repository.findOne(id);
			}

	- 通过@PersistenceContext注入指定的EntityManager，使用EntityManager进行数据库操作，例如

			@PersistenceContext(unitName = "order")
			private EntityManager orderEm;

			public List<Order> findAll() {
				return orderEm.createQuery("from Order", Order.class).getResultList();
			}

	- 注入EntityManagerHolderService，并通过它获取需要的EntityManager，再使用EntityManager进行数据库操作，例如

			private @Autowired EntityManagerHolderService emHolder;

			public Object method() {
				EntityManager orderEm = emHolder.getEntityManager("order"); // 获取order的EntityManager
				EntityManager defEm = emHolder.getDefaultEm(); // 获取默认的EntityManager
				... ...
			}

	2) 使用事务控制(此处介绍的是非jta事务，使用jta事务见[高级使用](./datasource-advance.md))

	- 默认数据源使用事务，只需在方法上加`@Transactional`注解

	- 其他数据源使用事务需要指定事务管理器，在方法上加`@Transactional("xxxTransactionManager")`注解，xxx为数据源名称。若不指定则使用默认事务管理器，对于该方法的事务控制将不起作用。

	- 一般的查询方法都不需要加事务控制，但如果你在你的方法里要获取操作配置了懒加载的属性，则需要事务管理，告诉懒加载器使用哪个EntityManger去加载属性。例如：
	
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

	- 当一个方法操作多个数据源并需要使用事务时，应使用jta事务，详见下一节[高级使用](./datasource-advance.md)，下面将举一反例：

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
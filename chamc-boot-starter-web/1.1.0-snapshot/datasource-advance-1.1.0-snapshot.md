# 高级使用

本节将介绍对于jta事务的使用。

## 配置

1) 同[基本使用](./datasource-base.md)中的配置，需要开启jta事务

	spring.jta.enabled=true

2) 如果某一个数据源不需要jta事务，可关闭该数据源的jta事务。例如：customer数据源不使用jta事务，则

	chamc.em.multi.ems.customer.tm.jta=false

## 使用事务控制（jta事务）

### 所有数据源均使用jta事务

1) 数据源使用`@Transactional`进行事务管理即可
 
2) 当一个方法操作多个数据源时，使用jta事务可进行跨数据源事务管理，例如：

	@Transactional
	public void orderException(ComposeModel compose) {
		if(Objects.isNull(compose.getOrder().getId())) {
			this.orderRepository.save(compose.getOrder());
		}
		if(Objects.isNull(compose.getProduct().getId())) {
			this.supplierService.save(compose.getProduct().getSupplier());
			this.productService.save(compose.getProduct());
		}
		if(Objects.isNull(compose.getCustomer().getId())) {
			// 因为id为-1的CustomerDetail不存在，所以这里会报错
			// 但由于我们使用了jta事务，所以都会回滚
			compose.getCustomer().getDetail().setId(-1L);
			this.customerRepository.save(compose.getCustomer());
		}
	}

3) 使用jta事务之后，查询懒加载属性的问题仍然没法解决。因为使用了jta事务，全局就一个事务管理器，他并不知道当前的lazy属性应当从哪个数据库查。下面是一个反例：

	@Transactional(readOnly = true)
	public List<Product> findAll() {
		// 由于配置了懒加载，所以这里查出来的product的supplier属性都还没有值
		List<Product> products = this.repository.findAll();
		
		for(Product product : products) {
			// 这里会报错；
			// 因为懒加载在真正去查数据库的时候会用当前的EntityManager去查，但我们整个应用里有多个EntityManager了，所以会报错
			log.info(product.getSupplier().toString());
		}
		return products;
	}
	
所以应该按照上节所说的规范去做，**如果我们需要使用关联对象，查询时将其fetch出来**
	
	public List<Product> findAllAndFetchSupplier() {
		JPAQueryFactory qf = this.repository.getQueryFactory();
		// 在查询product的时候直接将supplier也fetch出来，所以就不存在lazy的问题，就不需要加事务
		JPAQuery<Product> fetchQuery = qf.select(product).from(product)
					.leftJoin(product.supplier, supplier) // 使用querydsl的join，必须重命名，第二个参数就是别名
					.fetchJoin();
		return fetchQuery.fetch();
	}

### 部分数据源使用jta事务

1) 对于使用了jta事务的数据源，使用`@Transactional`进行事务管理

2) 对于某个关闭了jta事务的数据源，当它使用事务时需要指定事务管理器，即在方法上使用`@Transactional()`注解时需要value参数（`@Transactional(value = "xxxTransactionManager")`其中xxx为数据源名称）。若不指定则使用默认事务管理器（只要开启了jta事务，那么默认的事务管理器就是jta事务管理器），对于该方法的事务控制将不起作用。例：

	@Transactional(value = "customerTransactionManager")
	public Customer save(Customer customer) {
		this.customerDetailRepository.save(customer.getDetail());
		return this.repository.save(customer);
	}

3) 当一个方法既操作了使用jta事务的数据源，也操作了未使用jta事务的数据源，在该方法上加@Transactional即可进行跨数据源事务管理。但是，对未使用jta事务的数据源的操作其实是另外的事务。例如：

	@Transactional
	public void orderException(ComposeModel compose) {
		if(Objects.isNull(compose.getOrder().getId())) {
			this.orderRepository.save(compose.getOrder());
		}
		if(Objects.isNull(compose.getCustomer().getId())) {
			this.customerService.save(compose.getCustomer());
		}
		if(Objects.isNull(compose.getProduct().getId())) {
			// 因为id为-1的Supplier不存在，所以这里会报错
			// 但customer数据源没有纳入jta事务管理，所以上面对customer数据库的操作并不会回滚
			compose.getProduct().getSupplier().setId(-1L);
			this.productService.save(compose.getProduct());
		}
	}

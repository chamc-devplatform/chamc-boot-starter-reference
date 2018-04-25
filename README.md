## 开发平台后端框架参考指南

### 作者

苗世鹏、唐红石、罗明强

### 版本

0.0.1-SNAPSHOT

## 1.文档简介

本节主要是开发平台后端框架参考指南的一个概述，你可以把它想象成本文档的一个内容索引。你可以顺序的阅读本文档，也可以跳过本节，如果你对本节没有兴趣的话。

### 1.1 关于本文档

开发平台后端框架参考指南最新版本发布地址：[https://chamc-devplatform.github.io/chamc-boot-starter-reference/](https://chamc-devplatform.github.io/chamc-boot-starter-reference/) ，本文档的副本你可以随便下载或分享给他人。

### 1.2 获取帮助

关于开发平台后端框架的任何问题，我们都乐于提供帮助！
- 查看[How-to](#how-to)章节，你可以找到大部分问题的答案；
- 发送邮件给[我](mailto:luomingqiang@chamc.com.cn)、[苗世鹏](mailto:miaoshipeng@chamc.com.cn) 或 [唐红石](mailto:tanghongshi@chamc.com.cn)；
- 电话给我、苗世鹏或唐红石，电话号码可在公司通讯录里查询，具体路径：华融资产-子公司-华融融通-软件开发部。

### 1.3 开始步骤

如果你刚开始使用开发平台后端框架，那么[这章](#get-start)应该对你很有帮助！

### 1.4 开发平台后端框架组件

如果你对开发平台后端框架已经有了初步的了解，想要进一步的了解与使用，那么[这章](#component)将为你详细介绍开发平台后端框架的各个组件：[web组件](#web)、[swagger组件](#swagger)、[工作流组件](#bpm)、[service组件](#starter-service)。

### 1.5 高级功能

## <span id="get-start">2 入门</span>

如果你刚开始使用开发平台后端框架，那么本节内容会对你有很大帮助！本节主要回答基本的“what”、“how”和“why”问题，以及一个简单的使用说明。然后我们会构建第一个基于开发平台后端框架的应用。

### <span id="content2_1">2.1 开发平台后端框架介绍</span>

1. 简介    
开发平台后端框架基于Spring实现，只需引入依赖便可使用。使用开发平台后端框架，你可以配置多数据源、实现单点登录、进行日志追踪、使用swagger查看接口、使用工作流。
2. 系统要求  
开发平台后端框架使用的Spring Boot的版本为：Spring Boot 1.5.4.RELEASE；JDK版本为：jdk1.8；MAVEN版本为：maven3.5.0。

### 2.2 环境安装

详见[公司网盘](http://hq-spsdocument/Documents/Forms/AllItems.aspx?RootFolder=%2FDocuments%2F4-%E8%BD%AF%E4%BB%B6%E5%BC%80%E5%8F%91%E9%83%A8%2F%E5%9F%B9%E8%AE%AD%2F%E5%BC%80%E5%8F%91%E5%B9%B3%E5%8F%B0%E5%90%8E%E7%AB%AF%E6%A1%86%E6%9E%B6%E5%8F%82%E8%80%83%E6%8C%87%E5%8D%97) - 开发平台后端框架环境搭建.html

### 2.3 第一个demo

#### 2.3.1 新建项目（两种方法）
##### 1. 在STS中新建项目 （此方法需[配代理](#daili)）
1） 打开STS，File —》New —》Spring Starter Project  

2） 填写Name、Group、Artifact、Package，Next —》Finish。例如：

	Name: demo-d
	Group: com.chamc
	Artifact: demo-d
	Package: com.chamc.demo

**注意：项目group应为 com.chamc，package应以com.chamc开头**  

3） next之后，选择自己需要的依赖，如MySQL、DevTools等，如图：

![](https://i.imgur.com/uE9fvcw.png)

finish，新建成功后，如图所示：

![](https://i.imgur.com/KCFKonw.png)

4） 因为父工程的版本为spring boot 1.5.4，此处最好将工程的版本改为1.5.4，打开pom.xml文件，将

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.9.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

改为

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.4.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

##### 2. 在官网新建项目
1） 打开[https://start.spring.io/](https://start.spring.io/)，填入Group和Artifact，可添加一些依赖（例如：MySQL等），点击Generate Project，将自动下载一个压缩包。**注意：项目group应为 com.chamc**  

![](https://i.imgur.com/xogLRCi.png)

2） 解压压缩包，因为父工程的版本为spring boot 1.5.4，此处最好将工程的版本改为1.5.4，打开/demo-1/pom.xml文件，将

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.9.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

改为

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.4.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

3） 打开STS，File —》import... —》Maven —》Existing Maven Projects —》next>。  

4） 选择项目的路径，finish。在STS中可见此项目，如下。  

![](https://i.imgur.com/D5xYDkh.png)

#### 2.3.2 添加依赖

1） 打开pom.xml在`<dependencies>`标签中添加开发平台web组件依赖 

	<dependency>
		<groupId>com.chamc.boot</groupId>
		<artifactId>chamc-boot-starter-web</artifactId>	
		<version>0.0.1-SNAPSHOT</version>		
	</dependency>

2） 在`<plugins>`中添加插件

	<plugin>
		<groupId>com.mysema.maven</groupId>
		<artifactId>apt-maven-plugin</artifactId>
		<version>1.1.3</version>
		<executions>
			<execution>
				<goals>
					<goal>process</goal>
				</goals>
				<configuration>
					<outputDirectory>src/main/querysdl</outputDirectory>
					<processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
				</configuration>
			</execution>
		</executions>
	</plugin>

3） 右键项目，选择Maven —》update project...。此时可能还会报错，这是因为没有安装lombok，请执行下一步操作。  

4） 在maven dependencies中找lombok.jar所在路径，在其路径下找到它并安装。安装成功后，右键项目Maven —》update project...。（安装一次即可）  

![](https://i.imgur.com/f1vvl29.png)

![](https://i.imgur.com/YGYzCw4.png)

#### 2.3.3 开始编码

1） 在application.properties中添加以下配置。

- 数据库信息（MySQL数据库安装包可通过[公司网盘](http://hq-spsdocument/Documents/Forms/AllItems.aspx?RootFolder=%2FDocuments%2F4-%E8%BD%AF%E4%BB%B6%E5%BC%80%E5%8F%91%E9%83%A8%2F%E5%9F%B9%E8%AE%AD%2F171013-SpringMVC%E5%92%8CJPA%E5%9F%BA%E7%A1%80-%E7%BD%97%E6%98%8E%E5%BC%BA%2F%E8%BD%AF%E4%BB%B6)获得）  

		spring.datasource.url=jdbc:mysql://localhost:3306/test?characterEncoding=utf8&useSSL=true
		spring.datasource.username=root
		spring.datasource.password=1111

- repository、entity扫描路径

		chamc.web.scan.repository=com.chamc.demo
		chamc.web.scan.entity=com.chamc.demo

- radis

		spring.redis.host=10.1.8.119
		spring.session.store-type=hash-map

2） 在mysql中建一个数据库**（注意：如果要使用代码生成功能，建表时，每个表必须有一个主键id，数据类型为Decimal(18,0)）**例如：  

- 新建数据库test

		CREATE SCHEMA `test3` ;

- 新建表t_user

		CREATE TABLE `t_user` (
		  `id` decimal(18,0) NOT NULL,
		  `username` varchar(50) DEFAULT NULL,
		  `password` varchar(50) DEFAULT NULL,
		  `userdetail_id` int(11) DEFAULT NULL,
		  PRIMARY KEY (`id`)
		) ENGINE=InnoDB DEFAULT CHARSET=utf8;

- 新建表t_userdetail

		CREATE TABLE `t_userdetail` (
		  `id` decimal(18,0) NOT NULL,
		  `name` varchar(45) DEFAULT NULL,
		  `birthday` date DEFAULT NULL,
		  `age` int(11) DEFAULT NULL,
		  PRIMARY KEY (`id`)
		) ENGINE=InnoDB DEFAULT CHARSET=utf8;

<span id="codegenerate">3） 生成代码，使用CodeGenerator.generate(……)方法。例如：  </span>

    /**
	 * @param fileAbsoluteOutPath 生成的文件绝对目录，如："D:/eclipse-jee-neon/framework/chamc-boot-starter-web/src/main/java"
	 * @param author 作者，如：luomq
	 * @param dbtype 数据库类型，如：DbType.MYSQL
	 * @param url 数据库连接url，如："jdbc:mysql://127.0.0.1:3306/spt?characterEncoding=utf8"
	 * @param driver 数据库驱动类名，如："com.mysql.jdbc.Driver"
	 * @param username 数据库用户名
	 * @param password 数据库密码
	 * @param tablePrefixs 表前缀，生成entity类名的时候，会剔除掉这个前缀
	 * @param tableNames 表名
	 * @param basePackage 包名，如：com.chamc.demo，则生成的entity会在com.chamc.demo.entity包下，repository会在com.chamc.demo.repository包下，service和controller同理
	 * @param entity 是否生成entity
	 * @param repository 是否生成repository
	 * @param service 是否生成service
	 * @param controller 是否生成controller
	 * @param auditable 是否继承AuditableEntity
	 */
	public static void main(String[] args) {
		CodeGenerator.generate(
				"D:/Program Files (x86)/sts-bundle/dev-platform-demo/demo-doc/src/main/java", 
				"tanghongshi", 
				DbType.MYSQL, 
				"jdbc:mysql://localhost:3306/test?characterEncoding=utf8&useSSL=true", 
				"com.mysql.jdbc.Driver", 
				"root", 
				"1111", 
				new String[]{"t_"}, 
				new String[]{
						"t_user","t_userdetail"
				}, 
				"com.chamc.demo1", 
				true, 
				true, 
				true, 
				true, 
				false);
	}

右键run as java application，控制台打印如下，则生成成功。

	11:04:41.417 [main] DEBUG com.chamc.boot.generator.AutoGenerator - ==========================文件生成完成！！！==========================

右键项目refresh一下，可见生成了controller、service、entity和repository的类，下图所示。  

![](https://i.imgur.com/f6bhDU2.png)

其中，  
controller负责具体的业务模块流程的控制，调用service中的方法，详细介绍请见[2.4.1 关于controller](#controller)  
service负责业务逻辑的实现，调用repository中的方法，详细介绍请见[2.4.2 关于service](#service)  
repository与数据库进行交互，详细介绍请见[2.4.3 关于repository](#repository)  
entity是实体，详细介绍请见[2.4.4 关于entity](#entity)  

4） 生成的controller不是rest接口，若想改为rest接口，将@Controller改为@RestController，将继承的BaseController改为BaseRestController，以UserController为例，如下：

	@RestController @RequiredArgsConstructor(onConstructor_=@Autowired)
	@RequestMapping("/user")
	public class UserController extends BaseRestController<User> {
		
		private final @Getter UserService service;
		
	}


5） 打开BaseRestController可见已实现一些接口：如增删改查。

6） 右键项目，选择Run as —》 Spring boot app，选择DemoDocApplication，OK.  

控制台打印如下，则启动成功  

	2017-12-21 11:20:07.680  INFO  1572 --- [  restartedMain] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
	2017-12-21 11:20:07.691  INFO  1572 --- [  restartedMain] com.chamc.demo.DemoDocApplication        : Started DemoDocApplication in 9.375 seconds (JVM running for 10.064)

7) 先在数据库t_user表中新增几条数据，使用postman（可通过[公司网盘](http://hq-spsdocument/Documents/Forms/AllItems.aspx?RootFolder=%2FDocuments%2F4-%E8%BD%AF%E4%BB%B6%E5%BC%80%E5%8F%91%E9%83%A8%2F%E5%9F%B9%E8%AE%AD%2F171013-SpringMVC%E5%92%8CJPA%E5%9F%BA%E7%A1%80-%E7%BD%97%E6%98%8E%E5%BC%BA%2F%E8%BD%AF%E4%BB%B6)获取，也可直接使用浏览器测试），访问`http://localhost:8080/user`（GET方法）查询所有用户，可见结果如下

	[
	    {
	        "id": 1,
	        "username": "test001",
	        "password": "111111",
	        "userdetailId": null,
	        "new": false
	    },
	    {
	        "id": 2,
	        "username": "test002",
	        "password": "222222",
	        "userdetailId": null,
	        "new": false
	    }
	]

同时还有以下接口可以使用：  
<pre>
（GET） http://localhost:8080/user  查询所有user  
（GET） http://localhost:8080/user/{id}  查询单个user 例如：http://localhost:8080/user/1  
（POST） http://localhost:8080/user  新增user 例如：http：//localhost:8080/user?username=test3&password=123456  
（PUT） http://localhost:8080/user/{id}  修改user 例如：http://localhost:8080/user/3?username=test3&password=654321  
（DELETE） http://localhost:8080/user/{id}  删除user 例如：http://localhost:8080/user/3    
（GET） http://localhost:8080/user 按某一字段查询（需添加@GlobalSearch） 例如：http://localhost:8080/user?search=1
（GET） http://localhost:8080/user/page 分页查询（可添加@GlobalSearch） 例如：http://localhost:8080/user/page?search=1&page=0&size=10
</pre>

添加@GlobalSearch，@GlobalSearch接受一个字符串数组，添加需要查询的字段名称，如下（配置按username查询）

	@Entity
	@Table(name = "t_user")
	@EqualsAndHashCode(callSuper = true)
	@GlobalSearch({"username"})
	public @Data class User extends BaseEntity {
	
		@Id @GeneratedValue
		private Long id;
		private String username;
		private String password;
		@Column(name = "userdetail_id")
		private Long userdetailId;
	
	}

查询username中包含1的用户，访问`http://localhost:8080/user/page?search=1&page=0&size=10`，search为匹配字段，即查询（select * from t_user where username like '%1%'），结果如下

	{
	    "content": [
	        {
	            "id": 1,
	            "username": "test001",
	            "password": "111111",
	            "userdetailId": null,
	            "new": false
	        }
	    ],
	    "last": true,
	    "totalPages": 1,
	    "totalElements": 1,
	    "number": 0,
	    "size": 10,
	    "sort": null,
	    "first": true,
	    "numberOfElements": 1
	}

8） 新增关联关系，每个user关联一个userdetail，如下所示

	@Entity
	@Table(name = "t_user")
	@EqualsAndHashCode(callSuper = true)
	@GlobalSearch({"username"})
	public @Data class User extends BaseEntity {

	    ... ...
		
		@OneToOne(cascade = CascadeType.ALL)
		@JoinColumn(name = "userdetail_id")
		private Userdetail detail;
	
	}

新增一个新的user，请求`(POST)http://localhost:8080/user?username=test003&password=123456&detail.name=测试三号&detail.birthday=1994-10-10&detail.age=24`，结果如下

	{
	    "id": 4,
	    "username": "test003",
	    "password": "123456",
	    "detail": {
	        "id": 6,
	        "name": "测试三号",
	        "birthday": "1994-10-10 00:00",
	        "age": 24,
	        "new": false
	    },
	    "new": false
	}

**【其他注意事项】**

- @OneToOne一对一，@ManyToOne多对一，@OneToMany一对多。
- Cascade是级联操作，比如一起删除，一起新增等。**（不推荐使用）**
- Fetch是抓取策略，即查询时机。
 - fetchType.EAGER代表立即查询，即查询了Userinfo立马根据Userinfo去查询Userdetail。
 - fetchType.LAZY代表延迟查询，即先不查询，先拿到Userinfo的数据，没有查询Userdetail的数据，当我们要使用Userdetail的时候再去查询.
- mappedBy只有OneToOne,OneToMany,ManyToMany上才有mappedBy属性，ManyToOne不存在该属性；
 - mappedBy标签一定是定义在the owned side(被拥有方的)，他指向the owning side(拥有方)；
 - 关系的拥有方负责关系的维护，在拥有方建立外键。所以用到@JoinColumn
 - mappedBy跟JoinColumn/JoinTable总是处于互斥的一方

<span id="jpa">9） 尝试自己写一个接口，按返回年龄小于X岁的所有用户信息并按倒序排列。  </span>

- 在UserController里添加方法

		@GetMapping("ageLt")
		public ResponseEntity<List<User>> ageLt(@RequestParam Long age){
			List<User> users = service.processAgeLtBusiness(age);
			return ResponseEntity.ok(users);
		}

- 在UserService里添加方法

		public List<User> processAgeLtBusiness(Long age) {
			return repository.findByDetailAgeLessThanOrderByDetailAgeDesc(age);
		}

- 在UserRepository里添加方法

		List<User> findByDetailAgeLessThanOrderByDetailAgeDesc(Long age);


请求`(GET)http://localhost:8888/user/ageLt?age=20`，运行效果如下：  

	[
	    {
	        "id": 1,
	        "username": "test001",
	        "password": "111111",
	        "detail": {
	            "id": 1,
	            "name": "测试1号",
	            "birthday": "2017-11-11 00:00",
	            "age": 1,
	            "new": false
	        },
	        "new": false
	    }
	]

**【其他注意事项】**  

- JPA学习资料请参考[Spring Data JPA - Reference Documentation](https://docs.spring.io/spring-data/jpa/docs/1.11.6.RELEASE/reference/html/#jpa.query-methods.query-creation) 
- 在service里面加对数据库的复杂操作。推荐使用querydsl写数据库语句，querydsl学习资料请参考[Querydsl Reference Guide](http://www.querydsl.com/static/querydsl/4.1.3/reference/html_single/)，示例请见[2.4](#querydsl)

### <span id="gecengjieshao">2.4 关于各层的介绍</span>

#### <span id="controller">2.4.1 关于controller</span>

1. 简介： controller层负责具体的业务模块流程的控制，controller层主要调用service层里面的方法控制具体的业务流程。

2. controller书写

 - 对于Rest接口，Controller请求方法返回类型应为ResponseEntity<T>类型
 
 - 方法体内尽可能三段（行）论：调用校验参数的函数、调用业务服务、产生结果、返回结果
    <pre>
	public class TestController extends BaseController {
	
		private final @Getter UserService service;
	
		@GetMapping("/findByUsername")
		public ResponseEntity findOrgByUsername(String username) {
			processFindOrgByUsernameParam(username);//校验入参数据并组装业务处理需要的数据

			Org org = processFindOrgByUsernameBussiness(username);
                   //调用业务处理方法，在processXXXBussiness中调用多个service方法
                   //如果业务逻辑比较简单，则可以直接调用service方法，例如：service.findByOrgByUsername(..)

			ResponseEntity result = processFindOrgByUsernameResult(org);//根据业务处理返回值组装返回给客户端的结果
			return result;
		}
	
	}
    </pre>
 - 实体类不能直接返回，需对入参和出参进行封装，在controller包下新建dto、param、result包，dto存放实体类的dto（可作为入参和出参），param存放入参，result存放出参，类的命名为：（入参）请求方法+controller方法名+param、（出参）请求方法+controller方法名+result，例如
 	
			@GetMapping("relatedList")
			public ResponseEntity<GetRelatedArchiveListResult> relatedArchiveList(Pageable pageable,GetRelatedArchiveListParam param){		
				processRelatedArchiveListParam(param.getArchiveDeptId(),param.getArchiveTypeId());
				Page<BaseDoc> baseDocs = service.processRelatedArchiveListBussiness(pageable,param);
				ResponseEntity<GetRelatedArchiveListResult> result = processRelatedArchiveListResult(baseDocs);
				return result;
			}

		dto与param&result的区别在于：dto是实体类的子类，如果入参（或出参）是某一实体类或其子类则新建一个DTO，例如：

			//实体类
			@Entity
			@Table(name = "t_user")
			public @Data class User {

				@Id
				@GeneratedValue(generator = "snowflake")
				@GenericGenerator(name = "snowflake", strategy = "com.chamc.boot.web.support.SnowflakeIdGenerator")
				@Column(name = "ID_")
				private Long id;

				@Column(name = "ACCOUNT_")
				private String account;

				@Column(name = "NAME_")
				private String name;

				@Column(name = "PASSWORD_")
				private String password;

				@Column(name = "MOBILE_")
				private String mobile;

			}

			//DTO
			public @Data class UserDTO {

				private Long id;
				private String name;
				private String mobile;
	
			}

 - 建议controller里不要调用controller，调用service
 - 当入参的类中存在List里嵌套List，传入参数解析可能会出现问题，可在配置文件application.properties中设置`chamc.method.complex-argument-support-types`，例如

			chamc.method.complex-argument-support-types=com.chamc.archives.archive.controller.param.PostDocArchiveDetailParam

3. Rest接口url书写规范

 - 单资源( singular-resourceX )

			url样例：order/  (order即指那个单独的资源X)   
			GET — 返回一个新的order  
			POST — 创建一个新的order，从post请求携带的内容获取值  
 - 单资源带id(singular-resourceX/{id} )

			URL样例：order/1 ( order即指那个单独的资源X )
			GET — 返回id是1的order
			DELETE — 删除id是1的order
			PUT — 更新id是1的order，order的值从请求的内容体中获取。
 - 复数资源(plural-resourceX/)

		    URL样例:orders/
			GET – 返回所有orders
 - 复数资源查找(plural-resourceX/search)

		    URL样例：orders/search?name=123
		    GET – 返回所有满足查询条件的order资源。(实例查询，无关联) – order名字等于123的。
 - 复数资源查找(plural-resourceX/searchByXXX)

			URL样例：orders/searchByItems?name=ipad
			GET – 将返回所有满足自定义查询的orders – 获取所有与items名字是ipad相关联的orders。
 - 单数资源(singular-resourceX/{id}/pluralY)

			URL样例：order/1/items/ (这里order即为资源X，items是复数资源Y)
			GET – 将返回所有与order id 是1关联的items。
 - singular-resourceX/{id}/singular-resourceY/

			URL样例：order/1/item/
			GET – 返回一个瞬时的新的与order id是1关联的item实例。
			POST – 创建一个与order id 是1关联的item实例。Item的值从post请求体中获取。
 - singular-resourceX/{id}/singular-resourceY/{id}/singular-resourceZ/

			URL样例：order/1/item/2/package/
			GET – 返回一个瞬时的新的与item2和order1关联的package实例。
			POST – 创建一个新的与item 2和order1关联的package实例，package的值从post请求体中获得。

	上面的规则可以在继续递归下去，并且复数资源后面永远不会再跟随负数资源。  
	总结几个关键点，来更清晰的表述规则。  
	△ 在使用复数资源的时候，返回的是最后一个复数资源使用的实例。  
	△ 在使用单个资源的时候，返回的是最后一个但是资源使用的实例。  
	查询的时候，返回的是最后一个复数实体使用的实例(们)。  

4. 参数验证： 详见[2.5 参数验证的使用与扩展](#validation)

#### <span id="service">2.4.2 关于service</span>

1. 简介：service负责实现业务逻辑，service调用repository中的方法，在service中进行事务控制。

2. 事务控制  
在service中进行事务控制，只需在service的方法上添加`@Transactional`，推荐在做数据库更新操作时都加上事务控制，注意，不推荐在service中调用service，这样做可能会让事务失效。例如：

		@Transactional
		public ResponseEntity<?> processModifyArchiveBussiness(BaseDoc baseDoc, PostDocArchiveDetailParam docArchive) {
		    ……
		}

#### <span id="repository">2.4.3 关于repository</span>

1. 简介：repository与数据库交互，可以看做是一个原子操作。

2. JPA的使用  

1） 使用方法  

a、属性名查询  
findByXxx，findByXxxLike，findByXxxAndYyy  
findBy可以替换成find、read、readBy、query、queryBy、get、getBy  
Xxx可以为：and，or，is，equals，between，lessthen，lessthanequal，greaterthen，greaterthenequal，after，before， isnull，isnotnull，notnull，like，notlike，startingwith，endingwith，containing，orderby，not，in，notin，true，false，ignorecase`  

b、限制结果数量  

		findFirst10ByName()//获得符合条件的前10条数据  
		findTop30ByName()//前30条数据 
 
如果要分页，则将pageable传入，例如：  

		Page<User> findByName(String name,Pageable pageable);

c、NamedQuery  
在entity中注解查询方法  

		@NamedQuery(name = "Person.findByName",query = "select p from Person p where p.name = ?1")

在Repository中定义方法  

		List<Person> findByName(String name);

d、使用`@Query`  
repository也可使用jpql语句进行查询，在repository方法上加`@Query`注解。  
查询参数：=?1 -> 第一个参数 或者 = :name -> 名字为name的参数`

		@Query("select u from User u where u.emailAddress = ?1")
		User findByEmailAddress(String emailAddress);
		
		@Query("select u from User u where u.firstname = :firstname or u.lastname = :lastname")
		User findByLastnameOrFirstname(@Param("lastname") String lastname, @Param("firstname") String firstname);

2） JPA学习资料请参考[Spring Data JPA - Reference Documentation](https://docs.spring.io/spring-data/jpa/docs/1.11.6.RELEASE/reference/html/#jpa.query-methods.query-creation)   

3） fetch的使用  

- 某些时候，一个对象关联多个对象，在查询该对象时，会将对象所关联的其他对象统统查出来，然而，我们并不希望这样，我们只想查出我们所需要的对象，在这种情况下，fetch就派上了用场。  

例如，user和role是一个多对多的关系，我们在查询user时，不希望查出role对象，则我们将FetchType设为Lazy，如图：

	    @ManyToMany(fetch = FetchType.LAZY)
		@JoinTable(name = "t_user_role",
				joinColumns = {@JoinColumn(name = "user_id",referencedColumnName = "id")},
				inverseJoinColumns = {@JoinColumn(name = "role_id",referencedColumnName = "id")})
		private List<Role> roles;

写一个接口根据用户id返回用户姓名，该接口并未使用role对象，所以不会去查询role表，如图：

		@GetMapping("{id}/name")
		public ResponseEntity<String> name(@PathVariable Long id){
			User user = service.findOne(id);
			if (user == null){
				return ResponseEntity.noContent().build();
			}
			return ResponseEntity.ok(user.getDetail().getName());
		}

sql语句只打印了一条  

		Hibernate: select user0_.id as id1_5_0_, user0_.userdetail_id as userdeta4_5_0_, user0_.password as password2_5_0_, user0_.username as username3_5_0_, userdetail1_.id as id1_7_1_, userdetail1_.age as age2_7_1_, userdetail1_.birthday as birthday3_7_1_, userdetail1_.name as name4_7_1_ from t_user user0_ left outer join t_userdetail userdetail1_ on user0_.userdetail_id=userdetail1_.id where user0_.id=?

- 有时我们也需要将关联对象查询出来，这时，我们应该在查询对象的时候join上关联对象的表。  
例如，写一个接口用角色code查用户名列表。

		@GetMapping("names")
		public ResponseEntity<List<String>> names(@RequestParam String roleCode){
			List<String> result = service.processNamesBussiness(roleCode);
			return ResponseEntity.ok(result);
		}

		public List<String> processNamesBussiness(String roleCode) {
			List<User> users = repository.findNamesByRolesCode(roleCode);
			if (users != null){
				List<String> names = new ArrayList<>();
				users.forEach(u->{
					names.add(u.getUsername());
				});
				return names;
			}
			return null;
		}

		@Query("select u from User as u join FETCH u.roles as role where role.code = ?")
		List<User> findNamesByRolesCode(String roleCode);

请求接口后，查看打印的sql，发现查询user时fetch了role表。

		Hibernate: select user0_.id as id1_6_0_, role2_.id as id1_5_1_, user0_.userdetail_id as userdeta4_6_0_, user0_.password as password2_6_0_, user0_.username as username3_6_0_, role2_.code as code2_5_1_, role2_.name as name3_5_1_, roles1_.user_id as user_id3_7_0__, roles1_.role_id as role_id2_7_0__ from t_user user0_ inner join t_user_role roles1_ on user0_.id=roles1_.user_id inner join t_role role2_ on roles1_.role_id=role2_.id where role2_.code=?
		Hibernate: select userdetail0_.id as id1_8_0_, userdetail0_.age as age2_8_0_, userdetail0_.birthday as birthday3_8_0_, userdetail0_.name as name4_8_0_ from t_userdetail userdetail0_ where userdetail0_.id=?
		Hibernate: select userdetail0_.id as id1_8_0_, userdetail0_.age as age2_8_0_, userdetail0_.birthday as birthday3_8_0_, userdetail0_.name as name4_8_0_ from t_userdetail userdetail0_ where userdetail0_.id=?

3. querydsl的使用

1） 使用方法

示例：查询年龄小于X岁的用户并按年龄倒序排列

	public List<User> processAgeLtBusinessByQsdl(Long age){
		AbstractJPAQuery<User, JPAQuery<User>> query = this.repository.createDslQuery();
		QUser qUser = QUser.user;
		JPQLQuery<User> jpqlQuery = query.select(qUser).from(qUser).where(qUser.detail.age.lt(age)).orderBy(qUser.detail.age.desc());
		return jpqlQuery.fetch();
	}

2） fetch的使用

示例：

	JPQLQuery<User> jpqlQuery = query.select(qUser).from(qUser).join(qUser.roles).fetchJoin().where(qUser.roles.contains(role));

    //多级关联
	JPQLQuery<Child> query = q.select(qchild).from(qchild)
			.leftJoin(qchild.parent, qparent).fetchJoin()
			.leftJoin(qparent.grand, qgrand).fetchJoin()
			.leftJoin(qgrand.ancestor, qancestor).fetchJoin()
			.where(qparent.id.eq(parentId)
					.and(qgrand.id.eq(grandId))
					.and(qancestor.id.eq(ancestorId)));


3） querydsl学习资料请参考[Querydsl Reference Guide](http://www.querydsl.com/static/querydsl/4.1.3/reference/html_single/)

#### <span id="entity">2.4.4 关于entity</span>

1. 简介：entity对应数据库中的表

2. entity中的注释介绍

 - @Entity注释指名这是一个实体Bean
 - @Table注释指定了Entity所要映射带数据库表，其中@Table.name()用来指定映射表的表名。如果缺省@Table注释，系统默认采用类名作为映射表的表名。实体Bean的每个实例代表数据表中的一行数据，行中的一列对应实例中的一个属性。
 - @Column注释定义了将成员属性映射到关系表中的哪一列和该列的结构信息，属性如下：

			  name：映射的列名，默认与数据库同名。若属性名为驼峰类型，默认找数据库中下划线类型，例如属性名为userName，则数据库中对应的字段为user_name；
			  unique：是否唯一；
			  nullable：是否允许为空；
			  length：对于字符型列，length属性指定列的最大字符长度；
			  insertable：是否允许插入；
			  updatetable：是否允许更新；

 - @Id注释指定表的主键。
 - @GeneratedValue注释定义了标识字段生成方式。
 - @GenericGenerator注释是hibernate所提供的自定义主键生成策略生成器，由@GenericGenerator实现多定义的策略。  
   
			使用CodeGenerator生成的entity使用snowflake策略生成主键，使主键全局唯一：
            @Id
	        @GeneratedValue(generator = "snowflake")
	        @GenericGenerator(name = "snowflake",strategy = "com.chamc.boot.web.support.SnowflakeIdGenerator")
	        private Long id;
   
 - @Temporal注释用来指定java.util.Date或java.util.Calender属性与数据库类型date、time或timestamp中的那一种类型进行映射。
 - @JoinColumn用来标明映射中的关系。

3. entity的使用规范

1） 属性名使用驼峰形式，对应数据库中的下划线形式。

2） 当使用实体关系映射时，fetch类型设置为lazy，当需要时再fetch。例：

		@ManyToMany(fetch = FetchType.LAZY)
	    @JoinTable(name = "t_user_role",
			joinColumns = {@JoinColumn(name = "user_id",referencedColumnName = "id")},
			inverseJoinColumns = {@JoinColumn(name = "role_id",referencedColumnName = "id")})
	    private List<Role> roles;

#### 2.5.1 参数验证的使用

- spring和web组件实现了一些简单的参数验证的方法，使得接口免去了一些重复的冗余的验证代码。常用的validation有：

		Bean Validation 中内置的 constraint         
		@Null   被注释的元素必须为 null    
		@NotNull    被注释的元素必须不为 null    
		@AssertTrue     被注释的元素必须为 true    
		@AssertFalse    被注释的元素必须为 false    
		@Min(value)     被注释的元素必须是一个数字，其值必须大于等于指定的最小值    
		@Max(value)     被注释的元素必须是一个数字，其值必须小于等于指定的最大值    
		@DecimalMin(value)  被注释的元素必须是一个数字，其值必须大于等于指定的最小值    
		@DecimalMax(value)  被注释的元素必须是一个数字，其值必须小于等于指定的最大值    
		@Size(max=, min=)   被注释的元素的大小必须在指定的范围内    
		@Digits (integer, fraction)     被注释的元素必须是一个数字，其值必须在可接受的范围内    
		@Past   被注释的元素必须是一个过去的日期    
		@Future     被注释的元素必须是一个将来的日期    
		@Pattern(regex=,flag=)  被注释的元素必须符合指定的正则表达式    
		    
		Hibernate Validator 附加的 constraint    
		@NotBlank(message =)   验证字符串非null，且长度必须大于0    
		@Email  被注释的元素必须是电子邮箱地址    
		@Length(min=,max=)  被注释的字符串的大小必须在指定的范围内    
		@NotEmpty   被注释的字符串的必须非空    
		@Range(min=,max=,message=)  被注释的元素必须在合适的范围内 

- 示例：

在入参的类里为需要验证的属性添加validation：

	public @Data class GetLoginParam {
	
		private @NotBlank String username;
		private @Length(min = 6,max = 16)String password;
	}

在controller方法的参数处添加`@Valid`：

	@GetMapping("login")
	public ResponseEntity<User> login(@Valid GetLoginParam param){
		
		return null;
	}

请求时，参数未通过验证就会报错：

	{
	    "timestamp": "2017-12-27 15:13",
	    "status": 400,
	    "error": "Bad Request",
	    "exception": "org.springframework.validation.BindException",
	    "errors": [
	        {
	            "codes": [
	                "Length.getLoginParam.password",
	                "Length.password",
	                "Length.java.lang.String",
	                "Length"
	            ],
	            "arguments": [
	                {
	                    "codes": [
	                        "getLoginParam.password",
	                        "password"
	                    ],
	                    "arguments": null,
	                    "defaultMessage": "password",
	                    "code": "password"
	                },
	                16,
	                6
	            ],
	            "defaultMessage": "长度需要在6和16之间",
	            "objectName": "getLoginParam",
	            "field": "password",
	            "rejectedValue": "123",
	            "bindingFailure": false,
	            "code": "Length"
	        }
	    ],
	    "message": "Validation failed for object='getLoginParam'. Error count: 1",
	    "path": "/user/login"
	}

#### 2.5.2 参数验证的扩展

- 示例：写一个验证，验证参数必须不为空且都为大写字母。如下：

1） 先写一个注解：

	@Target({ ElementType.FIELD, ElementType.PARAMETER })
	@Retention(RetentionPolicy.RUNTIME)
	@Constraint(validatedBy = UppercaseValidator.class)
	@Documented
	public @interface Uppercase {
	
	    String message() default "必须不为空且全为大写";
		
		Class<?>[] groups() default { };
	
		Class<? extends Payload>[] payload() default { };
		
	}

2） 再写一个类实现ConstraintValidator：

	public class UppercaseValidator implements ConstraintValidator<Uppercase,String>{
	
		@Override
		public void initialize(Uppercase constraintAnnotation) {
			// TODO Auto-generated method stub
			
		}
	
		@Override
		public boolean isValid(String value, ConstraintValidatorContext context) {
			if (value != null && value.length() > 0){
				for(int i = 0; i < value.length(); i++){
					if (Character.isUpperCase(value.charAt(i))) continue;
					else return false;
				}
				return true;
			}
			return false;
		}
	
	}

3） 按照上一点的使用方法使用即可。

## <span id="component">3 开发平台后端框架组件</span>

### <span id="web">3.1 web组件</span>

#### 3.1.0 基础信息配置 ####

    chamc.web.scan.repository=com.chamc.archive     #业务系统的repository的扫描配置，业务系统的repository不需要再加@Repository注解
    chamc.web.scan.entity=com.chamc.archive     #业务系统的entity扫描配置

#### 3.1.1 配置多数据源及其使用说明

1） 简介
  
该组件支持配置多个数据源，即可对多个数据库进行操作，详细使用方法见下。 

2） 配置  

- 在配置文件application.properties中，开启多数据源并配置默认数据源（**注意：如果之前配了数据库，需删除之前配置**），def指默认数据源，当没有指定数据源时，默认使用该数据源。默认数据源必配！

	    chamc.ds.compose.enable=true
	    chamc.ds.compose.def.url=jdbc:mysql://localhost:3306/test?characterEncoding=utf8&useSSL=true
	    chamc.ds.compose.def.username=root
	    chamc.ds.compose.def.password=1111

- 配置其他数据源，例如命名为：test，则配置如下

		chamc.ds.compose.data-sources.test.url=jdbc:mysql://localhost:3306/test2?characterEncoding=utf8&useSSL=true
		chamc.ds.compose.data-sources.test.username=root
		chamc.ds.compose.data-sources.test.password=1111

	test由自己定义，可再使用不同的命名继续增加数据源

- 除了以上配置，还有一个配置chamc.ds.compose.pointcut，表示对于@TargetDataSource注解的解析应用在哪些地方，默认是继承BaseService的类中@TargetDataSource生效。若不想继承BaseService也想使其生效，则需通过aspectJ配置方式进行配置。

3） demo

- 使用代码生成工具生成repository和entity，参见[2.3.1 — 4 — 3）](#codegenerate)
- 当需要使用test数据源时，在其service里的方法上加标签**@TargetDataSource**
- 例如：写一个接口，当type=0时返回用户信息，否则返回书的信息

在controller中：

		@GetMapping("test")
		public ResponseEntity<?> bookOrUsers(@RequestParam Long type){
			if (type.equals(0L)){
				List<User> users = service.findUsers();
				return ResponseEntity.ok(users);
			}else {
				List<Book> books = service.findBooks();
				return ResponseEntity.ok(books);
			}
		}

在service中：

		public List<User> findUsers() {
			return repository.findAll();
		}
	
		@TargetDataSource("test")
		public List<Book> findBooks() {
			return bookRepository.findAll();
		}

运行结果如下：

请求：`http://localhost:8080/user/test?type=0`

		[
		  {
		    "id": 1,
		    "username": "test001",
		    "password": "111111",
		    "userdetailId": 1,
		    "new": false
		  },
		  {
		    "id": 2,
		    "username": "test002",
		    "password": "222222",
		    "userdetailId": 2,
		    "new": false
		  }
		]

请求：`http://localhost:8080/user/test?type=1`

		[
		  {
		    "id": 1,
		    "name": "三国演义",
		    "price": 56,
		    "writer": "罗贯中",
		    "new": false
		  },
		  {
		    "id": 2,
		    "name": "测试书本",
		    "price": 12,
		    "writer": "测试",
		    "new": false
		  }
		]

**注意**：  
1. 当一个controller中需要使用多个数据源的数据，应该在controller中调用多个service方法，而不是在service中调用service  
2. 使用哪一个数据源进行操作，取决于第一次进入service中指定的数据源  
3. 不指定数据源时，均使用默认数据源  
4. 错误示例：controller调用service中的processBookOrUsers2Bussiness()方法后，数据源已经指定为默认数据源，在 processBookOrUsers2Bussiness()中再调用service的findBooks()方法，findBooks()上配置的test数据源将不起作用。数据源已第一次进入service时指定的数据源为准。 

controller中：

		@GetMapping("test")
		public ResponseEntity<?> bookOrUsers2(@RequestParam Long type){
			ResponseEntity<?> result = service.processBookOrUsers2Bussiness(type);
			return result;
		}
	
service中：

		public ResponseEntity<?> processBookOrUsers2Bussiness(Long type) {
			if (type.equals(0L)){
				List<User> users = this.findUsers();
				return ResponseEntity.ok(users);
			}else{
				List<Book> books = this.findBooks();
				return ResponseEntity.ok(books);
			}
		}

        public List<User> findUsers() {
			return repository.findAll();
		}
	
		@TargetDataSource("test")
		public List<Book> findBooks() {
			return bookRepository.findAll();
		}

#### 3.1.2 安全相关功能及其使用说明

1） 简介

该组件提供域登陆、权限控制、用户组织机构数据同步等安全相关功能，详细使用方法如下。

2） 域登陆配置

- 在application.properties文件中开启security，并配置不需要验证的url和不需要做验证登录的url，例如：

		chamc.security.enable=true
		chamc.security.permission.enable=true
		chamc.security.addtional-none-login-urls=/loginUrl

- 登录请求分为两种类型：ajax（rest请求，前后端分离）和normal（前后端不分离），配置如下：
 - ajax类型，url是ad登陆的服务url，appName为与系统标识名称，retUrl为回调地址。
 
			#ajax
			chamc.security.ad.login-type=ajax
			chamc.security.ad.url=http://10.1.8.81/ChamcSSO/LoginWin.ashx
			chamc.security.ad.app-name=TestApp
			chamc.security.ad.ret-url=http://localhost:8080/ajaxLogin

 - normal类型，需配置验证成功的跳转地址。

            #normal
			chamc.security.ad.login-type=normal
			chamc.security.ad.success-url=/index

- 用户信息会放入redis缓存里，所以也要设置redis地址：

			spring.redis.host=10.1.8.119

3） 域登陆demo

- 新建以下7张表：t_sys_org，t_sys_permission，t_sys_role，t_sys_role_permission，t_sys_user，t_sys_user_org，t_sys_user_role。在jar包中获取建表脚本:`init_sys_[mysql|oracle].sql`。

- 在库中插入数据，包括用户、角色、权限等。以下是可用的几个域用户，角色、机构、权限请自行插入：

		INSERT INTO `t_sys_user` (`id_`,`account_`,`name_`,`password_`,`email_`,`sort_order_`,`domain_account_`,`account_name_with_domain_`,`is_domain_account_`,`mobile_`,`home_phone_`,`room_no_`,`office_phone_`,`office_short_phone_`,`status_`,`gmt_create_`,`create_user_`,`gmt_update_`,`update_user_`,`gmt_status_up_`,`status_up_user_`,`gmt_status_down_`,`status_down_user_`) VALUES (1,'user1@kmdev.com.cn','用户1',NULL,'user1@dev.com',NULL,'dev\\user1',NULL,'1',NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL);
		INSERT INTO `t_sys_user` (`id_`,`account_`,`name_`,`password_`,`email_`,`sort_order_`,`domain_account_`,`account_name_with_domain_`,`is_domain_account_`,`mobile_`,`home_phone_`,`room_no_`,`office_phone_`,`office_short_phone_`,`status_`,`gmt_create_`,`create_user_`,`gmt_update_`,`update_user_`,`gmt_status_up_`,`status_up_user_`,`gmt_status_down_`,`status_down_user_`) VALUES (2,'leader1@kmdev.com.cn','领导1',NULL,'leader1@dev.com',NULL,'dev\\leader1',NULL,'1',NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL);
		INSERT INTO `t_sys_user` (`id_`,`account_`,`name_`,`password_`,`email_`,`sort_order_`,`domain_account_`,`account_name_with_domain_`,`is_domain_account_`,`mobile_`,`home_phone_`,`room_no_`,`office_phone_`,`office_short_phone_`,`status_`,`gmt_create_`,`create_user_`,`gmt_update_`,`update_user_`,`gmt_status_up_`,`status_up_user_`,`gmt_status_down_`,`status_down_user_`) VALUES (3,'user2@kmdev.com.cn','用户2',NULL,'user2@dev.com',NULL,'dev\\user2',NULL,'1',NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL);


- 写一个接口获取ad登录地址

		@GetMapping("loginUrl")
		public String loginUrl(){
			String loginUrl = securityProperties.getAd().getUrl() + "?appName=" + securityProperties.getAd().getAppName() 
		    + "&retUrl=" + securityProperties.getAd().getRetUrl();
			return loginUrl;	
		}

- 请求该接口，获取登录地址进行登录。

4） 权限控制

开启security之后，可使用注解进行权限控制，目前仅支持前后端不分离的模式，前端模板使用thymeleaf。

有三种安全注解可供使用：`@Secured`注解、`@RolesAllowed`注解以及表达式驱动的注解（`@PreAuthorize`、`@PostAuthorize`、`@PreFilter`和`@PostFilter`）。推荐使用表达式驱动的注解。

**【准备工作】**

 - 第一步：建表，新建以下7张表：t_sys_org，t_sys_permission，t_sys_role，t_sys_role_permission，t_sys_user，t_sys_user_org，t_sys_user_role。在jar包中获取建表脚本:`init_sys_[mysql|oracle].sql`。

 - 第二步：同步用户、组织数据，插入角色、权限等数据。注意：权限表和角色表中的`status_`为1时，表示该权限或角色启用。

**【同步用户-组织机构数据】**

**【1】**同步程序设计说明

- 同步程序需要多数据源的支持，数据源来自人力系统`EntUserDb`,目标数据库是业务系统数据库，entuserdb和sys的数据库er图如下所示；
- 同步模块可配置使用或关闭，如下“使用demo”中的配置项所示；
- 同步程序以两种方式提供服务，定时任务+service调用；
- 同步程序使用多线程处理，相对来说效率较高，线程池可配置，如下“使用demo”中的配置项所示，多线程处理后会等待处理结果，然后再继续执行下一步逻辑；
- 同步程序可以添加干预listener，只需要继承`SyncOperationListenerAdapter`或者实现`SyncOperationListener`即可；
- 同步按照`机构 -> 用户 -> 用户机构`或者`用户 -> 机构 -> 用户机构`的顺序执行，

![entuserdb ER图](https://i.imgur.com/IS2yDal.jpg)

<center>图：EntUserDb ER图</center>

![t_sys_er图](https://i.imgur.com/IhZMUbt.jpg)

<center>图：sys_ ER图</center>

**【2】**使用

前置条件：1、已根据第一步的数据库初始化脚本在数据库中间表；

步骤1：在项目的`application.propertis`中添加配置项，如下所示：

    chamc.security.permission.enable=true   #是否启用permission模块
    chamc.web.permission.sync.operatorId=1  #同步的操作人id
    #chamc.web.permission.sync.timer.enable=true     #（可选）是否启用定时同步任务，默认为false
    #chamc.web.permission.sync.timer.cron=0 27 11 17 1 ?    #（可选）定时任务的任务计划，为cron表达式，默认值为每天晚上九点同步（0 0 21 * * *），corn说明参考[3、cron简单示例]
    #chamc.web.permission.sync.thread.core-pool-size=50     #（可选）同步程序线程池配置，实例中数字为默认值
    #chamc.web.permission.sync.thread.max-pool-size=200     #（可选）
    #chamc.web.permission.sync.thread.queue-capacity=500        #（可选）

步骤2：运行同步程序

如果添加配置项的时候，配置了启用定时同步任务（`chamc.web.permission.sync.timer.enable=true`），则会自动按照定时任务计划（`cron`）运行同步程序，不需要再添加其他代码调用。

ps：enteruserdb数据源为sqlserver数据库，请注意为业务系统添加sqlserver依赖，如下所示：

    <dependency>
    	<groupId>com.microsoft.sqlserver</groupId>
    	<artifactId>sqljdbc4</artifactId>
    	<version>4.0</version>
        <scope>runtime</scope>
    </dependency>

如果需要手动调用同步程序，只需要在业务系统代码中注入对应的`syncService`并调用同步方法即可，如下所示：

    @RestController
    public class SyncController {

    	//import com.chamc.boot.web.support.sys.service.SyncService;
    	private @Autowired SyncService service;
    	
    	/**
    	 * 同步机构（包含enteruserdb中对应的role）
    	 */
    	@GetMapping("org")
    	public void syncOrg() {
    		service.syncOrg();
    	}
    	
    	/**
    	 * 同步用户
    	 */
    	@GetMapping("user")
    	public void syncUser() {
    		service.syncUser();
    	}
    	
    	/**
    	 * 同步"用户-机构关系"
    	 */
    	@GetMapping("userorg")
    	public void syncUserOrg() {
    		service.syncUserOrg();
    	}
    	
    	/**
    	 * 同步"用户，机构（enteruserdb的角色），用户-机构关系"
    	 */
    	@GetMapping("sync")
    	public void sync() {
    		service.sync();
    	}
    	
    	@GetMapping("syncTest")
    	public void syncTest() {
    		service.syncThread();
    	}
    }

如果需要对同步程序进行干预，可采用以下方式添加bean并注入到系统中，可以有多个listener共存，共同起作用：

    - 方法1：继承`SyncOperationListenerAdapter`实现需要的方法
    - 方法2：继承`SyncOperationListener`并实现其中的方法

示例：

    /**
     * beforeOrg方法：方法体中可以修改org，该org为直接写入到数据库的org，如果需要过滤掉该org返回false，否则需要返回true
     * beforeUser方法：同beforeOrg，order为监听顺序
    **/
    //listener
    public class SyncListener extends SyncOperationListenerAdapter {

        //顺序默认为999，从小到大排列
    	@Override
    	public int order() {
    		return super.order();
    	}

    	@Override
    	public boolean beforeOrg(Org org) {
    		return super.beforeOrg(org);
    	}
    
    	@Override
    	public boolean beforeUser(User user) {
    		return super.beforeUser(user);
    	}
    }

    //listener1
    public class SyncListener1 implements SyncOperationListener {

        
    	@Override
    	public int order() {
    		return 0;
    	}

    	@Override
    	public boolean beforeUser(User user) {
    		if(user.getAccount().equals("xiaojia@chamc.com.cn@4869")) {
    			log.info("syncListener1");
    			return true;
    		}
    		return false;
    	}
    
    	@Override
    	public boolean beforeOrg(Org org) {
    		if(org.getCode().equals("ORG100000012")) {
    			return true;
    		}
    		return false;
    	}
    
    	@Override
    	public void afterThrowing(Object obj, Throwable t) {
    	}
    }


    //注入 listener（**仅供参考**）
    @Configuration
    public class SyncAutoConfig {

    	public @Bean SyncListener syncListener() {
    		return new SyncListener();
    	}
    	
    	public @Bean SyncListener1 syncListener1() {
    		return new SyncListener1();
    	}
    }
    


**【3】**<span id="cron">cron简单示例</span>

ps：可网上在线搜索“cron在线表达式”，在线获得表达式，粘贴使用即可，另[cron说明直通车](http://blog.csdn.net/u011116672/article/details/52517247)

cron表达式为6位的、用空格分隔的字符串

    0 0 21 * * *

如上示例，6位的表达式，分别为`second minute hour day month weekday`；  

|----|----|----|----|----|
|类别|是否必须|值范围|可用符号|附注|
|second|是|0-59|`*` `,` `-` `\`|`\`表示每隔多久执行|
|minute|是|0-59|`*` `,` `-` `\`|`*`表示通配任意匹配，`,`表示可以有多个值，用`,`隔开，`-`表示一个区间范围|
|hour|是|0-23|`*` `,` `-` `\`||
|day|是|1-31|`*` `,` `-` `\` `?`|`?`表示当同时配置了星期时，与星期互斥，即是这一天但不是星期n的时候，在这一天执行|
|month|是|1-12/JAN-DEC|`*` `,` `-` `\`||
|weekday|是|1-7/SUN-SAT|`*` `,` `-` `\` `?`|1表示星期一|

示例：

0 0 21 * * *：表示每天晚上九点整执行任务  
\30 0 21 * * *：表示每天晚上九点的第一分钟，每隔30秒执行一次任务  
0 0-30 21,22 * * *:表示每天晚上九点到九点半、十点到十点半都执行任务

**【后端权限控制】**

在方法上加注解来控制方法的权限。 

**[1]** `@Secured`和`@RolesAllowed`类似，使用一个String数组作为参数，每个String值是一个权限，调用这个方法至少需要具备其中的一个权限。例如以下两个例子，用户要访问add方法必须有`ADD`角色或者`ADMIN`角色之一；用户要访问delete方法必须有`DELETE`角色或者`ADMIN`角色之一。
<pre>
@Secured({"ROLE_ADD","ROLE_ADMIN"})
public String add(){
   ……
   return "add";
}

@RolesAllowed({"ROLE_DELETE","ROLE_ADMIN"})
public String delete(){
   ……
   return "delete";
}
</pre>

这两个注解再拒绝未认证用户方面还不错，但是都存在一个缺点：它们只能根据用户有没有授予特定的权限来限制方法的调用，在判断方式是否执行方面，无法使用其他的因素。为此，Spring Security 3.0引入了表达式驱动的注解。

**[2]** 表达式驱动的注解：这些注解的值参数中都可以接受一个SpEL表达式（[了解更多](https://docs.spring.io/spring-security/site/docs/5.0.0.RELEASE/reference/htmlsingle/#el-access)）。表达式可以是任意合法的SpEL表达式，如果表达式的计算结果为true，那么安全规则通过，否则就会失败。安全规则通过或失败的结果会因为所使用注解的差异而有所不同。

**【常用的几个EL表达式】**
<pre>
hasAnyRole([role1,role2])  //如果用户被授予了列表中任意的指定角色，结果为true
hasRole([role])            //如果用户被授予了指定的角色，结果为true
hasAuthority([authority])  //如果用户有指定的权限，结果为true
hasAnyAuthority([authority1,authority2])  //如果用户有列表中任意的权限，结果为true
</pre>

示例：
<pre>
@PreAuthorize("hasRole('ADMIN')")
public String list(){……}

@PreAuthorize("hasAnyAuthority('project:view','project:*')")
public String list(){……}
</pre>

**【四个注解】**
<pre>
@PreAuthorize   //在方法调用之前，基于表达式的计算结果来限制对方法的访问
@PostAuthorize  //允许方法调用，但是如果表达式计算结果为false，将抛出一个安全性异常
@PostFilter     //允许方法调用，但必须按照表达式来过滤方法的结果
@PreFilter      //允许方法调用，但必须在进入方法之前过滤输入值
</pre>

**@PreAuthorize**  表达式会在方法调用之前执行，如果表达式的计算结果不为true的话，将会阻止方法执行。与`@Secured`和`@RolesAllowed`相比，除了可以限制角色以外，还可以进行参数的验证。例如：  
<pre>
@PreAuthorize("hasAuthority('project:view') and #size>0")
@GetMapping("view")
public String list(int size) {
   return "project";
}
</pre>  
用户有`project:view`权限，并且入参size>0时，才能访问该方法。

**@PostAuthorize**  表达式直到方法返回才会执行，然后决定是否抛出安全性的异常。除了验证的时机之外，`@PostAuthorize`与`@PreAuthorize`的工作方式差不多，只不过它会在方法执行之后，才会应用安全规则。例如：

    @PostAuthorize("returnObject.size() == #size")
	@GetMapping("postAuth")
	public List<Book> testPost(int size) {
		……
		return books;
	}

`returnObject`表示返回的对象，本例中若返回列表中对象的个数与size不等，将抛出异常。

**@PostFilter**  会使用这个表达式计算该方法所返回集合的每个成员，将计算结果为false的成员移除掉。例如：

**@PreFilter**  过滤的是要进入方法中的集合成员，只有满足SpEL表达式的元素才会留在集合中。

**【前端的权限控制】**

在标签中加`sec:authorize`属性，其值也是SpEL表达式。例如：`<li sec:authorize="hasAuthority('project:view')">查看项目</li>`，当用户有`project:view`的权限，这个`<li>`才会显示在页面中。

**【相关类介绍】**

- com.chamc.boot.web.utils.SecurityUtils：提供获取当前用户信息（包括用户信息，角色及权限信息，主机构，兼职机构）和切换机构的方法。

		static com.chamc.boot.web.support.sys.service.LoginUser getCurrentUser();
		static void switchOrg(Long orgId); //orgId为目标机构Id

- LoginUser：当前用户信息

		com.chamc.boot.web.support.sys.model.User getUser(); //用户信息
		List<org.springframework.security.core.GrantedAuthority> getAuthorites(); //权限信息：包括角色和权限
		List<com.chamc.boot.web.support.sys.model.Org> getConcurrentOrgs(); //用户兼职机构
		List<com.chamc.boot.web.support.sys.model.Org> getOrgs(); //用户的所有机构
		Optional<com.chamc.boot.web.support.sys.model.Org> getMainOrg(); //用户的主机构（可为空）
        Optional<Org> getCurrentOrg();  //获取当前机构
        Optional<List<Role>> getCurrentRoles(); //获取当前角色
        Optional<List<Role>> getRoles();    //获取所有角色

#### 3.1.3 配置日志打印及其使用说明

1） 简介  

该组件提供对各层（controller、service、repository）进行日志跟踪，详细使用方法如下。

2） 配置

- 在application.properties文件中开启日志跟踪：

    	chamc.tracelog.enable=true

请求某个接口后，如下：

		2017-12-27 16:12:07.608 TRACE 988 --- [nio-8080-exec-9] c.c.b.w.config.log.TraceLogInterceptor   : Entering UserController.bookOrUsers(Long)
		2017-12-27 16:12:07.612 TRACE 988 --- [nio-8080-exec-9] c.c.b.w.config.log.TraceLogInterceptor   : Entering UserService.findBooks()
		2017-12-27 16:12:07.620 TRACE 988 --- [nio-8080-exec-9] c.c.b.w.config.log.TraceLogInterceptor   : Entering .Proxy192.findAll()
		Hibernate: select book0_.id as id1_4_, book0_.name as name2_4_, book0_.price as price3_4_, book0_.writer as writer4_4_ from t_book book0_
		2017-12-27 16:12:07.714 TRACE 988 --- [nio-8080-exec-9] c.c.b.w.config.log.TraceLogInterceptor   : Leaving  .Proxy192.findAll, took 94ms
		2017-12-27 16:12:07.714 TRACE 988 --- [nio-8080-exec-9] c.c.b.w.config.log.TraceLogInterceptor   : Leaving  UserService.findBooks, took 102ms
		2017-12-27 16:12:07.714 TRACE 988 --- [nio-8080-exec-9] c.c.b.w.config.log.TraceLogInterceptor   : Leaving  UserController.bookOrUsers, took 106ms

- 同时可以对各层进行配置：

![](https://i.imgur.com/8e6mqVi.png)

其中XXXX代表controller、service、repository：  
 
		chamc.tracelog.XXXX.enable /**是否开启，默认false*/
		chamc.tracelog.XXXX.showEnter /** 是否打印Enter日志 ，默认true */
		chamc.tracelog.XXXX.showExit /** 是否打印Exit日志 */
		chamc.tracelog.XXXX.className /** 日志内容是否包含类名，默认true */
		chamc.tracelog.XXXX.methodName /** 日志内容是否包含方法名，默认true */
		chamc.tracelog.XXXX.arguments /** Enter日志内容是否包含参数值，谨慎开启，可能会影响性能 ，默认false */
		chamc.tracelog.XXXX.argumentTypes /** Enter日志内容是否包含参数类型，默认true*/
		chamc.tracelog.XXXX.returnValue /** Exit日志内容是否包含返回值，谨慎开启，肯会影响性能 ，默认false */
		chamc.tracelog.XXXX.invocationTime /** Exit日志是否包含执行时间，单位：ms ，默认true */
		chamc.tracelog.XXXX.pointcut /** 日志切入点 */

3） demo  

在application.properties配置如图所示：

		chamc.tracelog.enable=true
		chamc.tracelog.controller.enable=true
		chamc.tracelog.service.enable=true
		chamc.tracelog.service.return-value=true
		chamc.tracelog.repository.enable=true
		chamc.tracelog.repository.arguments=true

请求一个接口，service的返回值和repository的入参被跟踪打印，如下：

		2017-12-27 16:22:33.064 TRACE 988 --- [io-8080-exec-10] c.c.b.w.config.log.TraceLogInterceptor   : Entering UserController.bookOrUsers(Long)
		2017-12-27 16:22:33.077 TRACE 988 --- [io-8080-exec-10] c.c.b.w.config.log.TraceLogInterceptor   : Entering UserService.findBooks()
		2017-12-27 16:22:33.086 TRACE 988 --- [io-8080-exec-10] c.c.b.w.config.log.TraceLogInterceptor   : Entering .Proxy202.findAll()()
		2017-12-27 16:22:33.129  INFO 988 --- [io-8080-exec-10] o.h.h.i.QueryTranslatorFactoryInitiator  : HHH000397: Using ASTQueryTranslatorFactory
		Hibernate: select book0_.id as id1_4_, book0_.name as name2_4_, book0_.price as price3_4_, book0_.writer as writer4_4_ from t_book book0_
		2017-12-27 16:22:33.135 TRACE 988 --- [io-8080-exec-10] c.c.b.w.config.log.TraceLogInterceptor   : Leaving  .Proxy202.findAll(), took 49ms
		2017-12-27 16:22:33.135 TRACE 988 --- [io-8080-exec-10] c.c.b.w.config.log.TraceLogInterceptor   : Leaving  UserService.findBooks with return value [Book(id=1, name=三国演义, price=56, writer=罗贯中), Book(id=2, name=测试书本, price=12, writer=测试)], took 58ms
		2017-12-27 16:22:33.135 TRACE 988 --- [io-8080-exec-10] c.c.b.w.config.log.TraceLogInterceptor   : Leaving  UserController.bookOrUsers, took 71ms

### <span id="swagger">3.2 swagger组件</span>

#### 3.2.1 简介
  - 本组件集成了Swagger。Swagger 是一个规范和完整的框架，用于生成、描述、调用和可视化 RESTful 风格的 Web 服务。总体目标是使客户端和文件系统作为服务器以同样的速度来更新。文件的方法，参数和模型紧密集成到服务器端的代码，允许API来始终保持同步。Swagger 让部署管理和使用功能强大的API从未如此简单。了解更多请到[https://swagger.io/](https://swagger.io/)。  
  - 关于Swagger UI官方解释是这样的：*The Swagger UI is an open source project to visually render documentation for a Swagger defined API directly from the API’s Swagger specifcation*。Swagger可以将某种固定格式的JSON数据生成可以视图的在线API文档，支持在线测试，可以清楚的观察到IO数据。
  - 使用本组件可以获得在线API文档，并能在线测试，节省编写接口文档的时间。

#### 3.2.2 配置

1） 添加依赖

在pom.xml中的`<dependencies>`标签中添加依赖

	<dependency>
		<groupId>com.chamc.boot</groupId>
		<artifactId>chamc-boot-starter-swagger</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</dependency>

2） 修改配置文件

在application.properties文件中，增加配置，例如：

	chamc.swagger.enable=true
	chamc.swagger.apis.user.group=User
	chamc.swagger.apis.user.path=/user/**
	chamc.swagger.apis.user.title=\u7528\u6237\u63A5\u53E3

 - enable=true表示使用swagger组件  
 - user为自定义表示一个group，可添加多个group的配置，命名不可相同
  - user.group表示这个组的名字
  - user.path表示这个组映射的路径，可填多个用“,”分隔
  - user.title表示这个组api文档的标题  

#### 3.2.3 demo  
1） 示例  
按照以上说明，修改demo-1的配置，启动demo-1，访问`http://localhost:8080/swagger-ui.html`。api文档的标题、介绍已省略。  

除此界面以外，还提供另一版本，访问`http://localhost:8080/swagger-ui-3.html`，将检索框url改为`/v2/api-docs?group=User`，group的值为配置文件中设置的group的值。

2） 使用

展开各个接口，输入parameter，点击try it out进行请求。

![](https://i.imgur.com/swqW04D.png)
![](https://i.imgur.com/gOFF6H7.png)

3） 常用标签   
- 在swagger-annotations的包中可见有以下标签  
![](https://i.imgur.com/yxjd8Ie.png)

- 最常用的5个注解  
@Api：修饰整个类，描述Controller的作用  
@ApiOperation：描述一个类的一个方法，或者说一个接口  
@ApiParam：单个参数描述  
@ApiModel：用对象来接收参数  
@ApiProperty：用对象接收参数时，描述对象的一个字段   

- 一些简单使用例子

		@GetMapping("ageLt")
		@ApiOperation("查询年龄小于age的用户并按倒序排列")
		public ResponseEntity<List<User>> ageLt(@RequestParam @ApiParam(name = "age",value = "年龄",required = true) Long age){
			List<User> users = service.processAgeLtBusiness(age);
			return ResponseEntity.ok(users);
		}

		@ApiModel(value="Book", description="书")
		public @Data class Book extends BaseEntity {
		
			@Id @GeneratedValue
			@ApiModelProperty(value = "ID",example = "1")
			private Long id;
			@ApiModelProperty(value = "书名",example = "这是一本书")
			private @NotBlank String name;
			@ApiModelProperty(value = "价格",example = "11")
			private @Min(0) Long price;
			@ApiModelProperty(value = "作者",example = "佚名")
			private @Length(max=100) String writer;
		
		}

详细请参考：[https://github.com/swagger-api/swagger-core/wiki/Annotations#apimodel](https://github.com/swagger-api/swagger-core/wiki/Annotations#apimodel)、[http://www.cnblogs.com/softidea/p/6251249.html](http://www.cnblogs.com/softidea/p/6251249.html)


### <span id="bpm">3.3 工作流组件</span>

详情请参考
- [开发平台后端框架参考指南-流程引擎模块.html](docs/开发平台后端框架参考指南-流程引擎模块.html)
- 网盘文件[http://hq-spsdocument/_layouts/15/DocIdRedir.aspx?ID=C2A742TNNUZA-1797567310-1214](http://hq-spsdocument/_layouts/15/DocIdRedir.aspx?ID=C2A742TNNUZA-1797567310-1214)

### <span id="starter-service">3.4 service组件</span>

#### 3.4.1 简介

使用service组件，你可以：  
1. 将你的服务注册到注册中心，使得其他应用可以调用你的服务（即REST接口）。  
2. 你的应用也可以通过service组件调用其他应用提供的服务（即REST接口）。

#### 3.4.2 使用

##### 3.4.2.1 如何注册服务

1） 添加依赖  

在pom.xml文件中引入chamc-boot-starter-service的依赖：

	<dependency>
		<groupId>com.chamc.boot</groupId>
		<artifactId>chamc-boot-starter-service</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</dependency>

2） 配置文件配置

在application.properties文件中添加配置：

	spring.application.name=                 //自定义应用名
	chamc.service.registry.token=            //应用对应token

【注】token获取：将应用名发给[唐红石](mailto:tanghongshi@chamc.com.cn)获取。

这样你的应用就注册到了注册中心，其他应用可以通过注册中心发现你的服务，从而调用你的服务。

##### 3.4.2.2 如何调用已注册的服务

假设已将应用provider注册，现在应用consumer需要调用provider中的服务。那么，在consumer中进行如下操作即可。

1. 按照以上配置对consumer进行配置。

2. 新建一个接口，并加注解`@org.springframework.cloud.netflix.feign.FeignClient(name = "xxx")`，其中的参数name必须与需要调用的应用的应用名（即配置文件中的`spring.application.name`）相同，并按照写REST接口的方法书写方法。例如：    

	假设服务提供方provider有如下接口：
		
		@GetMapping("hello")
		public ResponseEntity<String> hello() {
			return ResponseEntity.ok("Hello world!");
		}

    则服务消费者consumer应这样调用：

		@FeignClient(name = "provider")
		public interface Client1RemoteService {
		
			@GetMapping("hello")
			public String hello();
			
		}
		

3. 在需要使用provider服务的地方，注入上一步新建的接口Client1RemoteService即可，如下：

		private @Autowired Client1RemoteService client1;

4. 在application.properties里新增如下配置：

		chamc.service.feign.scan=com.chamc.xxx

	其中com.chamc.xxx为Client1RemoteService接口所在的包路径。实际应用中，配置所有该类接口所在的包路径。

#### 3.4.3 Feign的使用

整体来说，提供方接口怎么定义，消费方的接口也就怎么定义。

之前的demo只调用了简单的无参接口，下面将介绍几个复杂的出入参接口例子。

1. 带参接口

	假设服务提供方有如下接口：

		@PostMapping("person")
		public ResponseEntity<String> create(String name, int age) {
			……
		}

	则服务消费者应这样调用：

	    @PostMapping("/person")
		public String create(@RequestParam("name") String name, @RequestParam("age") int age);

	【注意】消费者必须在参数上增加`@RequestParam("xxx")`注解，其中xxx必须与提供方的参数名一致。

2. 带`@PathVariable`参数接口

	假设服务提供方有如下接口：

		@GetMapping("/person/{id}")
		public ResponseEntity<String> get(@PathVariable("id") int id) {
			……
		}

	则服务消费者应这样调用：

	    @GetMapping("/person/{id}")
		public String getPerson(@PathVariable("id") int id);

3. 返回Json接口

	假设服务提供方有如下接口：

		@GetMapping("/person/{id}")
		public ResponseEntity<Person> get(@PathVariable("id") int id) {
			……
		}

	Person：

		public @Data class Person {
			private Long id;
			private String name;
			private int age;
			
			@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss.S Z", timezone = "GMT+8")
			private Date birthday;
			private Address address;
		}
		
	Address：

		public @Data class Address {
			private String street;
			private int no;
		}

	则服务消费者应这样调用：

		@GetMapping("/person/{id}")
		public User user(@PathVariable("id") Long id);

	User：

		public @Data class User {
			private String name;
			private int age;
			
			@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss.S Z", timezone = "GMT+8")
			private Date birthday;
			private Address address;
		}
		
		public @Data class Address {
			private String street;
			private int no;
			private String city;
		}

	【注意】消费方可以只接收自己需要的数据，与提供方属性名一致即可。

4. 接收Json参数接口

	假设服务提供方有如下接口：

		@PostMapping("")
		public ResponseEntity<Person> create(@RequestBody Person person){
			return ResponseEntity.ok(person);
		}

	则服务消费者应这样调用：

		@PostMapping("")
		public User create(@RequestBody User user);

#### 3.4.4 其他配置介绍

1. `chamc.service.feign.log-level`：在消费方设置，用于指定消费方调用提供方服务时打印日志的级别，包括none、headers、basic、full四个级别。默认为none。

	full级别日志打印示例：

		[Client1RemoteService2#create] ---> POST http://provider/create HTTP/1.1       
		[Client1RemoteService2#create] Content-Type: application/json;charset=UTF-8
		[Client1RemoteService2#create] Content-Length: 86
		[Client1RemoteService2#create]
		[Client1RemoteService2#create] {"name":"abc","age":12,"birthday":null,"address":{"street":null,"no":123,"city":"BJ"}}
		[Client1RemoteService2#create] ---> END HTTP (86-byte body)
        ......// 以上是发送请求时的日志，以下是接收到返回结果的日志
		[Client1RemoteService2#create] <--- HTTP/1.1 200 (660ms)
		[Client1RemoteService2#create] content-type: application/json;charset=UTF-8
		[Client1RemoteService2#create] date: Fri, 02 Mar 2018 07:05:40 GMT
		[Client1RemoteService2#create] transfer-encoding: chunked
		[Client1RemoteService2#create] x-application-context: provider:8899
		[Client1RemoteService2#create] 
		[Client1RemoteService2#create] {"id":null,"name":"abc","age":12,"birthday":null,"address":{"street":null,"no":123}}
		[Client1RemoteService2#create] <--- END HTTP (84-byte body)


## <span id="how-to">4 “How-to”指南</span>

## 5 附录



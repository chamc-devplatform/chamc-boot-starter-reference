# 第一个Demo
## 新建数据库

以mysql数据库为例，在mysql中建一个数据库**（注意：表主键id，数据类型为bigint）**例如：

1） 新建数据库test

	CREATE SCHEMA `test` ;

2） 新建表t\_user

	CREATE TABLE `t_user` (
		`id_` bigint NOT NULL,
		`username_` varchar(50) DEFAULT NULL,
		`password_` varchar(50) DEFAULT NULL,
		`detail_id_` bigint DEFAULT NULL,
		PRIMARY KEY (`id_`)
	) ENGINE=InnoDB DEFAULT CHARSET=utf8;

3） 新建表t\_userdetail

	CREATE TABLE `t_userdetail` (
		`id_` bigint NOT NULL,
		`name_` varchar(45) DEFAULT NULL,
		`birthday_` date DEFAULT NULL,
		`age_` int(11) DEFAULT NULL,
		PRIMARY KEY (`id_`)
	) ENGINE=InnoDB DEFAULT CHARSET=utf8;

## 初始化项目
**1. 新建项目**

1） 打开STS，File —》New —》Spring Starter Project

2） 填写Name、Group、Artifact、Package，Next —》Finish。例如：

    Name: demo
    Group: com.chamc
    Artifact: demo
    Package: com.chamc.demo

**注意：项目group应为 com.chamc，package应以com.chamc开头**

3） next之后，选择自己需要的依赖，如MySQL、DevTools等，如图：

![](https://i.imgur.com/5H9ik3B.png)

finish，新建成功后，如图所示：

![](https://i.imgur.com/iThxjwO.png)

4） 因为父工程的版本为spring boot 1.5.4，此处最好将工程的版本改为1.5.4，打开pom.xml文件，将

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.4.RELEASE</version>
	</parent>

改为

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.4.RELEASE</version>
	</parent>

**2. 添加依赖**

1） 打开pom.xml在`<dependencies>`标签中添加开发平台web组件依赖

	<dependency>
		<groupId>com.chamc.boot</groupId>
		<artifactId>chamc-boot-starter-web</artifactId>
		<version>1.0.0.RELEASE</version>
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
## 编码

### 添加配置信息

在application.properties中添加以下配置

	//数据库连接信息
	spring.datasource.url=jdbc:mysql://localhost:3306/test?characterEncoding=utf8&useSSL=true
	spring.datasource.username=root
	spring.datasource.password=123456

	//repository、entity扫描路径
	chamc.web.scan.repository=com.chamc.demo
	chamc.web.scan.entity=com.chamc.demo

	//redis信息
	spring.redis.host=10.1.8.119
	spring.session.store-type=hash-map

### 构建entity

**entity是实体，对应数据库中的表。**

对应于数据库 `test` 中的表 `t_user` 和 `t_userdetail`，需要分别建立entity `User` 和 `Userdetail`，如下所示：
	
![](https://i.imgur.com/yMVkhFo.png)

其实User如下所示：
		
	@Entity
	@Table(name = "t_user")
	public @Data class User {

		@Id @GeneratedValue(generator = "snowflake")
		@GenericGenerator(name = "snowflake", strategy = "com.chamc.boot.web.support.SnowflakeIdGenerator")
		@Column(name = "id_")
		private Long id;

		@Column(name = "username_")
		private String username;

		@Column(name = "password_")
		private String password;

		@OneToOne(fetch = FetchType.LAZY)
		@JoinColumn(name = "DETAIL_ID_")
		private Userdetail detail;
	}

Userdetail如下所示：

	@Entity
	@Table(name = "t_userdetail")
	public @Data class Userdetail {

		@Id @GeneratedValue(generator = "snowflake")
		@GenericGenerator(name = "snowflake", strategy = "com.chamc.boot.web.support.SnowflakeIdGenerator")
		@Column(name = "id_")
		private Long id;

		@Column(name = "name_")
		private String name;

		@Column(name = "birthday_")
		private Date birthday;

		@Column(name = "age_")
		private Long age;
	}

**其中entity中的注解解释如下**

   * @Entity注解指名这是一个实体Bean
   * @Table注解指定了Entity所要映射的数据库表
   * @Id注解指定表的主键
   * @GeneratedValue注解定义了主键生成器
   * @GenericGenerator注解定义了主键生成策略
   * @Column注解定义了将成员属性映射到关系表中的哪一列
   * @OneToOne注解表明实体`User` 和 `Userdetail`之间的关联关系是一对一，fetchType.LAZY代表加载方式是延迟加载
   * @JoinColumn定义了关联字段
   
#### 构建repository

**repository居于数据层和业务层之间，与数据库进行交互。**

对应于entity `User` 和 `Userdetail`，需要分别建立repository `UserRepository` 和 `UserdetailRepository`  如下所示：

![](https://i.imgur.com/mQVqAQn.png)	

其实UserRepository如下所示：

	public interface UserRepository extends BaseRepository<User> {

	}

UserdetailRepository如下所示：

	public interface UserdetailRepository extends BaseRepository<Userdetail> {

	}

repository构建时可以继承BaseRepository，BaseRepository中封装了一些管理和操作实体的基本方法。

### 构建service

**service负责实现业务逻辑，service调用repository中的方法。**
对应于entity `User` 和 `Userdetail`，可以建立`UserService ` 如下所示：

![](https://i.imgur.com/gqE78rI.png)

`UserService`需声明`UserRepository`和`UserdetailRepository`从而调用其方法，如下所示：
	
	@Service
	public class UserService {
		private @Autowired UserRepository userRepository;
		private @Autowired UserdetailRepository userDetailRepository;
	}

### 构建controller

**controller层负责具体的业务模块流程的控制，controller层主要调用service层里面的方法控制具体的业务流程。**

对应于entity `User` 和 `Userdetail`，可以建立`UserController` 如下所示:

![](https://i.imgur.com/eg8F1gl.png)

`UserController`也许声明`UserService`从而调用其中方法，如下所示：

	@Controller
	@RequestMapping("/user")
	public class UserController {
		private @Autowired UserService service;
	}

其中：@RequestMapping("/user")注解声明接口路径和Rest接口风格。

### 示例

**下面以实体User的增加，查询和删除为例，分别介绍repository、service和controller的代码实现。**

1 User保存

 * UserRepository:继承的`BaseRepository`中已有save的实现。
 * UserService：调用`UserRepository`中的save方法，**对于增加方法操作需要在Service中进行事务控制，即在方法上添加注解`@Transactional`**，示例代码如下：

		@Transactional
		public void save(User user) {
			this.userRepository.save(user);
		}

 * UserController：调用`UserService`中的save方法，**Rest接口返回类型应为ResponseEntity类型**，且方法体内应尽可能遵循三段论模式：入参处理、调用业务逻辑、结果参数处理，示例代码如下：

 	1）定义UserDTO进行参数传递，如下：

	![](https://i.imgur.com/6i3qUQa.png)
	
		public @Data class UserDTO {
			private Long id;
			private String username;
			private String password;
			private Userdetail detail;
		}

 	2）`UserController`中保存的实现，如下：

		@GetMapping
		public ResponseEntity<String> save(@RequestBody UserDTO dto) {
			User user = processSaveParam(dto); //入参处理
			service.save(user); //业务实现
			return ResponseEntity.ok("OK");//结果返回
		}
	
		//入参处理函数
		private User processSaveParam(UserDTO dto) {
			return BeanUtils.copyPropertiesWithClass(dto, User.class);
		}

@RequestBody注解表明入参形式为Json格式。

2 根据username查询User

 * UserRepository:可以使用JPA方法名的方法是进行声明查询方法， **JPA方法名的介绍可以参考后面的介绍**，示例代码如下：
 
		User findByUsername(String username);

 * UserService：调用`UserRepository`中的findByUsername方法，示例代码如下：

		public User findByUsername(String username) {
			return this.userRepository.findByUsername(username);
		}

 * UserController：调用`UserService`中的findByUsername方法，示例代码如下：

		@GetMapping
		public ResponseEntity<UserDTO> findByUsername(String username) {
			User user = service.findByUsername(username);//业务实现
			UserDTO dto = processFindByUsernameResult(user);//处理结果
			return ResponseEntity.ok(dto);//结果返回
		}
	
		//出参处理函数
		private UserDTO processFindByUsernameResult(User user) {
			return BeanUtils.copyPropertiesWithClass(user, UserDTO.class);
		}
	
3 User删除

 * UserRepository:继承的`BaseRepository`中已有delete的实现。
 * UserService：调用`UserRepository`中的delete方法，**对于删除方法操作需要在Service中进行事务控制**，示例代码如下：
	
		@Transactional
		public void delete(Long id) {
			this.userRepository.delete(id);
		}

 * UserController：调用`UserService`中的delete方法，示例代码如下：
	
		@DeleteMapping()
		public ResponseEntity<String> delete(Long id) {
			service.delete(id);//业务实现
			return ResponseEntity.ok("OK");//结果返回
		}

4 接口调试

**接口实现后可以使用swagger调试接口。**
 
* 添加依赖：打开pom.xml在`<dependencies>`标签中添加开发平台Swagger组件依赖;

		<dependency>
			<groupId>com.chamc.boot</groupId>
			<artifactId>chamc-boot-starter-swagger</artifactId>
			<version>1.0.0.RELEASE</version>
		</dependency>

* 配置文件：在application.properties中添加以下配置；

		chamc.swagger.enable=true
		chamc.swagger.apis.user.group=user
		chamc.swagger.apis.user.path=/user/**

* 调试接口：访问地址[http://localhost:8080/swagger-ui.html](http://localhost:8080/swagger-ui.html) 

**至此已经完成了对开发平台后端框架的入门介绍，后面将对开发平台后端框架的每个模块进行展开介绍。**

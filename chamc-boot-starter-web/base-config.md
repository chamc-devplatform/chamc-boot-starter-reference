# 基础信息配置

## pom文件基础配置 ##

父工程的版本为spring boot 1.5.4，最好将工程的spring boot版本改为1.5.4。

```
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>1.5.4.RELEASE</version>
</parent>
```

开发平台web组件依赖
```	
<dependency>
	<groupId>com.chamc.boot</groupId>
	<artifactId>chamc-boot-starter-web</artifactId>
	<version>0.0.1-SNAPSHOT</version>
</dependency>
```
swagger依赖
```
<dependency>
	<groupId>com.chamc.boot</groupId>
	<artifactId>chamc-boot-starter-swagger</artifactId>
	<version>0.0.1-SNAPSHOT</version>
</dependency>
```
Jpa查询插件配置
```
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
				<outputDirectory>src/main/querydsl</outputDirectory>
				<processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
			</configuration>
		</execution>
	</executions>
</plugin>
```
## application.properties配置 ##

### mysql数据库连接配置 ###

	spring.datasource.url=jdbc:mysql://localhost:3306/test?characterEncoding=utf8&useSSL=true
	spring.datasource.username=root
	spring.datasource.password=1111

如果不配置数据库连接信息，则会报错：  

	Cannot determine embedded database driver class for database type NONE

### repository、entity扫描路径 ###

说明：配置扫描路径越精确越好，最好指定到对应的repository和entity包下，多个包之间用 `,`分隔

	#业务系统的repository的扫描配置，业务系统的repository不需要再加@Repository注解
	chamc.web.scan.repository=com.chamc.demo.repository
	#业务系统的entity扫描配置
	chamc.web.scan.entity=com.chamc.demo.entity

如果不进行扫描包配置，启动时报错：    

	Could not resolve placeholder 'chamc.web.scan.repository' in value "${chamc.web.scan.repository}"

### radis配置 ###
	
说明：使用域登陆，缓存等功能时必须指定redis配置，否则会报`Cannot get Jedis connection`错误。如果不配置host，默认为本地的redis,
	
	spring.redis.host=127.0.0.1
	spring.session.store-type=redis

如果不进行redis的配置或未指定`store-type`，会抛异常：

	org.springframework.beans.factory.BeanCreationException: 
		Error creating bean with name 'org.springframework.boot.autoconfigure.session.SessionAutoConfiguration$SessionRepositoryValidator'
	java.lang.IllegalArgumentException: No Spring Session store is configured

### 不同环境配置文件 ###

说明：项目启动时，会默认读配置文件`application.properties`，但开发、测试、仿真、生产环境中的配置不同，频繁修改文件比较麻烦，我们可以提前配置好每个环境的配置文件，部署时只需指定用哪个配置文件即可。

新建四个配置文件，分别对应开发，测试，仿真，生产环境，在`application.properties`中指定即可使用。

	application-dev.properties（开发环境配置文件）   在application.properties配置spring.profiles.active=dev使其生效

	application-test.properties（测试环境配置文件）  在application.properties配置spring.profiles.active=test使其生效

	application-bak.properties（仿真环境配置文件）   在application.properties配置spring.profiles.active=bak使其生效

	application-prod.properties（生产环境配置文件）  在application.properties配置spring.profiles.active=prod使其生效


### swagger配置 ###

	chamc.swagger.enable=true
	chamc.swagger.apis.user.group=User
	chamc.swagger.apis.user.path=/user/**,teacher/**

swagger相关配置详见[Swagger组件](https://chamc-devplatform.gitbook.io/chamc-boot-starter-reference/chamc-boot-starter-swagger)的介绍

### 用户中心配置 ###

如果程序注册到了注册中心，则默认使用用户中心进行用户同步，需要配置用户中心token，否则项目无法启动，提示异常：

	Caused by: com.chamc.boot.web.support.BussinessException: 未配置用户中心token,请到服务管理系统：http://10.80.37.133:8080/services/home 申请token


### 配置文件加密 ###

配置文件中的一些配置可能会包含个人信息，例如用户名、密码配置等，为了防止信息的泄露需要对配置文件的一些信息进行加密处理，步骤如下：

* 1.配置文件配置：在配置文件中增加加解密使使用的密码配置，如下：

```
jasypt.encryptor.password="your password" 
```

* 2.生成密钥：构建测试类生成密钥

以数据库用户名和密码加密为例，可构建如下测试类生成用户名和密码对应密钥：

	@SpringBootTest
	@RunWith(SpringRunner.class)
	public class EncryptorConfigTest {

		private @Autowired StringEncryptor encryptor;

		@Test
		public void getPass() {
			String DBname = encryptor.encrypt("your DBname");
			String DBpassword = encryptor.encrypt("your DBpassword");
			System.out.println("DBname is " + DBname);
			System.out.println("DBpassword is " + DBpassword);
		}

	}


对应密钥如下：

![](https://i.imgur.com/2i7TxYb.png)

* 3.生成密钥写入配置文件：用上一步中生成的密钥替换配置文件中的相应信息**(密钥需写在ENC()的括号内)**，以数据库用户名和密码为例，修改配置文件如下：

		spring.datasource.username=ENC(Pw77huIjS3pFeFhgA/N7UoB2q1+E+M1A)
		spring.datasource.password=ENC(9qbeQj66mt+cnKIIZR8bX2A9L7wDtC+G)

这样简单的三步就完成了配置文件加密操作。

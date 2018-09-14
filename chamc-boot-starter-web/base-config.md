### 基础信息配置

1. pom文件基础配置

	* 父工程的版本为spring boot 1.5.4，最好将工程的spring boot版本改为1.5.4。

	* 开发平台web组件依赖
	
			<dependency>
				<groupId>com.chamc.boot</groupId>
				<artifactId>chamc-boot-starter-web</artifactId>
				<version>0.0.1-SNAPSHOT</version>
			</dependency>

	* swagger依赖

	        <dependency>
				<groupId>com.chamc.boot</groupId>
				<artifactId>chamc-boot-starter-swagger</artifactId>
				<version>0.0.1-SNAPSHOT</version>
			</dependency>

	* Jpa查询插件配置

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

2. application.properties配置

	* mysql数据库连接配置

			spring.datasource.url=jdbc:mysql://localhost:3306/test?characterEncoding=utf8&useSSL=true
	   		spring.datasource.username=root
	    	spring.datasource.password=1111

	* repository、entity扫描路径
  		- 配置扫描路径越精确越好，最好指定到对应的repository和entity包下，多个包之间用 `,`分隔
  
				chamc.web.scan.repository=com.chamc.demo.repository #业务系统的repository的扫描配置，业务系统的repository不需要再加@Repository注解
				chamc.web.scan.entity=com.chamc.demo.entity  #业务系统的entity扫描配置

	* radis
	
	    - 使用域登陆，缓存等功能时必须指定redis配置，否则会报`Cannot get Jedis connection`错误。如果不配置host，默认为本地的redis,
	
		    	spring.redis.host=10.1.8.119
		    	spring.session.store-type=hash-map


3. 项目启动时，会默认读配置文件`application.properties`，但开发、测试、仿真、生产环境中的配置不同，频繁修改文件比较麻烦，我们可以提前配置好每个环境的配置文件，部署时只需指定用哪个配置文件即可。

    新建四个配置文件，分别对应开发，测试，仿真，生产环境，在`application.properties`中指定即可使用。

    	application-dev.properties（开发环境配置文件）   在application.properties配置spring.profiles.active=dev使其生效

    	application-test.properties（测试环境配置文件）  在application.properties配置spring.profiles.active=test使其生效

    	application-bak.properties（仿真环境配置文件）   在application.properties配置spring.profiles.active=bak使其生效

    	application-prod.properties（生产环境配置文件）  在application.properties配置spring.profiles.active=prod使其生效

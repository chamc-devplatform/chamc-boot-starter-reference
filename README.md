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

如果你对开发平台后端框架已经有了初步的了解，想要进一步的了解与使用，那么[这章](#component)将为你详细介绍开发平台后端框架的各个组件：[web组件](#web)、[swagger组件](#swagger)、[工作流组件](#bpm)。

### 1.5 高级功能

## <span id="get-start">2 入门</span>

如果你刚开始使用开发平台后端框架，那么本节内容会对你有很大帮助！本节主要回答基本的“what”、“how”和“why”问题，以及一个简单的使用说明。然后我们会构建第一个基于开发平台后端框架的应用。

### <span id="content2_1">2.1 开发平台后端框架介绍</span>

1. 简介    
开发平台后端框架基于Spring实现，只需引入依赖便可使用。
2. 系统要求  
开发平台后端框架使用的Spring Boot的版本为：Spring Boot 1.5.4.RELEASE；JDK版本为：jdk1.8；MAVEN版本为：maven3.5.0。

### 2.2 环境安装

详见[公司网盘](http://hq-spsdocument/Documents/Forms/AllItems.aspx?RootFolder=%2FDocuments%2F4-%E8%BD%AF%E4%BB%B6%E5%BC%80%E5%8F%91%E9%83%A8%2F%E5%9F%B9%E8%AE%AD%2F171013-SpringMVC%E5%92%8CJPA%E5%9F%BA%E7%A1%80-%E7%BD%97%E6%98%8E%E5%BC%BA%2F%E8%BD%AF%E4%BB%B6)开发平台后端框架环境搭建.html

### 2.3 第一个demo

#### 2.3.1 新建项目（两种方法）
##### 1. 在STS中新建项目 （此方法需[配代理](#daili)）
1） 打开STS，File —》New —》Spring Starter Project  

![](https://i.imgur.com/yorOwni.png)

2） 填写信息，Next —》Finish。在STS中可见此项目，如下。**注意：项目group应为 com.chamc**  

![](https://i.imgur.com/IwqTOoy.png) 

![](https://i.imgur.com/KCFKonw.png)

##### 2. 在官网新建项目
1） 打开[https://start.spring.io/](https://start.spring.io/)，填入Group和Artifact，可添加一些依赖（例如：mysql等），点击Generate Project，将自动下载一个压缩包。**注意：项目group应为 com.chamc**  

![](https://i.imgur.com/xogLRCi.png)

2） 解压压缩包，因为父工程的版本为spring boot 1.5.4，此处最好将工程的版本改为1.5.4，打开/demo-1/pom.xml文件，修改如图所示。

![](https://i.imgur.com/Re76Sva.png)

3） 打开STS，File —》import... —》Maven —》Existing Maven Projects —》next>。  

![](https://i.imgur.com/KQ36uFH.png)

4） 选择项目的路径，finish。在STS中可见此项目，如下。  

![](https://i.imgur.com/Mej1yAz.png)

![](https://i.imgur.com/D5xYDkh.png)

#### 2.3.2 添加依赖

1） 打开pom.xml在`<dependencies>`标签中添加开发平台web组件依赖，若没有后端开发平台框架组件请先[获取](#get-web)  

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

![](https://i.imgur.com/TRvxJpW.png) 

4） 在maven dependencies中找lombok.jar所在路径，在其路径下找到它并安装。安装成功后，右键项目Maven —》update project...。（安装一次即可）  

![](https://i.imgur.com/f1vvl29.png)

![](https://i.imgur.com/YGYzCw4.png)

#### 2.3.3 开始编码

1） 在application.properties中添加数据库信息（MySQL数据库安装包可通过[公司网盘](http://hq-spsdocument/Documents/Forms/AllItems.aspx?RootFolder=%2FDocuments%2F4-%E8%BD%AF%E4%BB%B6%E5%BC%80%E5%8F%91%E9%83%A8%2F%E5%9F%B9%E8%AE%AD%2F171013-SpringMVC%E5%92%8CJPA%E5%9F%BA%E7%A1%80-%E7%BD%97%E6%98%8E%E5%BC%BA%2F%E8%BD%AF%E4%BB%B6)获得）  

![](https://i.imgur.com/acl5F7C.png)

2） 在mysql中建一个数据库**（注意：如果要使用代码生成功能，建表时，每个表必须有一个主键id，并且auto_increment）**例如：  

![](https://i.imgur.com/KN2eVWi.png)

![](https://i.imgur.com/diMhiYW.png)

![](https://i.imgur.com/blvD7uf.png)

<span id="codegenerate">3） 生成代码，新建一个generate包，新建一个Generator类，添加一个main方法，使用CodeGenerator.generate(……)。右键run as java application  </span>

![](https://i.imgur.com/16HnSPI.png)

右键项目refresh一下，可见下图所示。  

![](https://i.imgur.com/eucG6TS.png)

4） 生成的controller不是rest接口，若想改为rest接口，将@Controller改为@RestController，将继承的BaseController改为BaseRestController，如下图所示。

![](https://i.imgur.com/l7fXK7U.png)

5） 打开BaseRestController可见已实现一些接口：如增删改查。

![](https://i.imgur.com/JkvftvX.png)

6） 右键项目，选择Run as —》 Spring boot app，选择Demo1Application，OK.

![](https://i.imgur.com/bsNzUI9.png)

![](https://i.imgur.com/kUYA5N8.png)

启动之后，可能报错

![](https://i.imgur.com/XLMQtiT.png)

session store type使用来存放session的存储方式，目前Spring boot中只支持redis方式。由于本应用暂无需将session放入redis的需求，故这里就可以将session store type设置为none，在application.properties文件中添加`spring.session.store-type=none`，重启应用

![](https://i.imgur.com/nCwwjG3.png)

启动成功...

![](https://i.imgur.com/bE4ij3J.png)

7) 先在数据库t_user表中新增几条数据，使用postman（可通过[公司网盘](http://hq-spsdocument/Documents/Forms/AllItems.aspx?RootFolder=%2FDocuments%2F4-%E8%BD%AF%E4%BB%B6%E5%BC%80%E5%8F%91%E9%83%A8%2F%E5%9F%B9%E8%AE%AD%2F171013-SpringMVC%E5%92%8CJPA%E5%9F%BA%E7%A1%80-%E7%BD%97%E6%98%8E%E5%BC%BA%2F%E8%BD%AF%E4%BB%B6)获取，也可直接使用浏览器测试），访问`http://localhost:8080/user`（GET方法）查询所有用户，可见结果如下图

![](https://i.imgur.com/4Y22TFk.png)

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

添加@GlobalSearch：在实体类中，添加需要查询的字段名称，如下图（配置按username查询）

![](https://i.imgur.com/e18H5DT.png)

访问`http://localhost:8080/user/page?search=1&page=0&size=10`，结果如下图

![](https://i.imgur.com/uqrnZCF.png)

8） 新增关联关系，每个user关联一个userdetail，如图所示

![](https://i.imgur.com/Y4XyGTj.png)

cascade表示级联操作，如一起新增、一起修改、一起删除等。新增一个新的user，如下图

![](https://i.imgur.com/As2g0xx.png)

**【其他注意事项】**

- @OneToOne一对一，@ManyToOne多对一，@OneToMany一对多。
- Cascade是级联操作，比如一起删除，一起新增等。
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


运行效果如图所示：  

![](https://i.imgur.com/mzGIq5S.png)

**【其他注意事项】**  

- JPA学习资料请参考[Spring Data JPA - Reference Documentation](https://docs.spring.io/spring-data/jpa/docs/1.11.6.RELEASE/reference/html/#jpa.query-methods.query-creation) 
- 在service里面加对数据库的复杂操作。推荐使用querydsl写数据库语句，querydsl学习资料请参考[Querydsl Reference Guide](http://www.querydsl.com/static/querydsl/4.1.3/reference/html_single/)，示例请见[2.4](#querydsl)

#### <span id="get-web">2.3.4 获取组件</span>

1） 到私服[http://10.80.38.200:8081/#browse/browse/components:maven-snapshots](http://10.80.38.200:8081/#browse/browse/components:maven-snapshots)下载，将chamc-boot-starter-web文件夹下载到本地。

2） 打开本地maven仓库，在Preferences —》Maven —》User Settings 中查看本地仓库的目录，如C:\Users\tanghongshi\.m2\repository。

![](https://i.imgur.com/blBvUrj.png)

3） 根据pom.xml文件中添加的依赖的groupId、artifactId和版本号进行目录创建，比如，web组件的groupId为`<groupId>com.chamc.boot</groupId>`，artifactId为`<artifactId>chamc-boot-starter-web</artifactId>`，版本为`<version>0.0.1-SNAPSHOT</version>`，则目录为com\chamc\boot\chamc-boot-starter-web\0.0.1-SNAPSHOT，如图：

![](https://i.imgur.com/YXky7L6.png)

将maven-metadata.xml放入chamc-boot-starter-web文件夹中（如上图），将jar包和pom文件放入0.0.1-SNAPSHOT文件夹中，如图：

![](https://i.imgur.com/3aMadNV.png)

### <span id="querydsl">2.4 关于数据库操作的一些介绍</span>

#### 2.4.1 使用JPA

1） 示例请见，[2.3.1 — 4 — 9）](#jpa)  
2） JPA学习资料请参考[Spring Data JPA - Reference Documentation](https://docs.spring.io/spring-data/jpa/docs/1.11.6.RELEASE/reference/html/#jpa.query-methods.query-creation) 
3） fetch的使用  

- 某些时候，一个对象关联多个对象，在查询该对象时，会将对象所关联的其他对象统统查出来，然而，我们并不希望这样，我们只想查出我们所需要的对象，在这种情况下，fetch就派上了用场。  
例如，user和role是一个多对多的关系，我们在查询user时，不希望查出role对象，则我们将FetchType设为Lazy，如图：

![](https://i.imgur.com/gXi8eN1.png)

写一个接口根据用户id返回用户姓名，该接口并未使用role对象，所以不会去查询role表，如图：

![](https://i.imgur.com/fCcwLdu.png)

sql语句只打印了一条  
![](https://i.imgur.com/pu1FRkL.png)

- 有时我们也需要将关联对象查询出来，这时，我们应该在查询对象的时候join上关联对象的表。  
例如，写一个接口用角色code查用户名列表。

![](https://i.imgur.com/ZPsS2Ew.png)

![](https://i.imgur.com/ylfpRCJ.png)

![](https://i.imgur.com/j5w88aO.png)

请求接口后，查看打印的sql，发现查询user时fetch了role表。

		Hibernate: select user0_.id as id1_6_0_, role2_.id as id1_5_1_, user0_.userdetail_id as userdeta4_6_0_, user0_.password as password2_6_0_, user0_.username as username3_6_0_, role2_.code as code2_5_1_, role2_.name as name3_5_1_, roles1_.user_id as user_id3_7_0__, roles1_.role_id as role_id2_7_0__ from t_user user0_ inner join t_user_role roles1_ on user0_.id=roles1_.user_id inner join t_role role2_ on roles1_.role_id=role2_.id where role2_.code=?
		Hibernate: select userdetail0_.id as id1_8_0_, userdetail0_.age as age2_8_0_, userdetail0_.birthday as birthday3_8_0_, userdetail0_.name as name4_8_0_ from t_userdetail userdetail0_ where userdetail0_.id=?
		Hibernate: select userdetail0_.id as id1_8_0_, userdetail0_.age as age2_8_0_, userdetail0_.birthday as birthday3_8_0_, userdetail0_.name as name4_8_0_ from t_userdetail userdetail0_ where userdetail0_.id=?

#### 2.4.2 使用querydsl

1） 示例：查询年龄小于X岁的用户并按年龄倒序排列

	public List<User> processAgeLtBusinessByQsdl(Long age){
		AbstractJPAQuery<User, JPAQuery<User>> query = this.repository.createDslQuery();
		QUser qUser = QUser.user;
		JPQLQuery<User> jpqlQuery = query.select(qUser).from(qUser).where(qUser.detail.age.lt(age)).orderBy(qUser.detail.age.desc());
		return jpqlQuery.fetch();
	}

2） querydsl学习资料请参考[Querydsl Reference Guide](http://www.querydsl.com/static/querydsl/4.1.3/reference/html_single/)

### <span id="validation">2.5 参数验证的使用与扩展</span>

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

![](https://i.imgur.com/Usw6Nc0.png)

在controller方法的参数处添加`@Valid`：

![](https://i.imgur.com/HU5GvKn.png)

请求时，参数未通过验证就会报错：
例如将入参中的price填为-1，就会报错，提示price不能小于0

![](https://i.imgur.com/I8LFelD.png)

![](https://i.imgur.com/wvF0tsc.png)

#### 2.5.2 参数验证的扩展

- 示例：写一个验证，验证参数必须不为空且都为大写字母。如下：

先写一个注解：

![](https://i.imgur.com/38dMFfR.png)

再写一个类实现ConstraintValidator：

![](https://i.imgur.com/CetgECz.png)

使用：

在role的code上加`@Uppercase`标签：

![](https://i.imgur.com/BjzM6Fh.png)

在controller方法的参数处添加`@Valid`：

![](https://i.imgur.com/2PYYN4k.png)

请求时，当code为小写时，将报错：

![](https://i.imgur.com/LMAMMsx.png)

code为大写时，请求成功：

![](https://i.imgur.com/ezRW11u.png)

## <span id="component">3 开发平台后端框架组件</span>

### <span id="web">3.1 web组件</span>

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

3） demo

- 使用代码生成工具生成repository和entity，参见[2.3.1 — 4 — 3）](#codegenerate)
- 当需要使用test数据源时，在其service里的方法上加标签**@TargetDataSource**
- 例如：写一个接口，当type=0时返回用户信息，否则返回书的信息

在controller中：

![](https://i.imgur.com/TbXZnw3.png)

在service中：

![](https://i.imgur.com/OebEjTv.png)

运行结果如下：

![](https://i.imgur.com/uIDAY6V.png)

![](https://i.imgur.com/r78mqPr.png)

**注意**：  
1. 当一个controller中需要使用多个数据源的数据，应该在controller中调用多个service方法，而不是在service中调用service  
2. 使用哪一个数据源进行操作，取决于第一次进入service中指定的数据源  
3. 不指定数据源时，均使用默认数据源  
4. 错误示例  

![](https://i.imgur.com/EwzZW9n.png)
![](https://i.imgur.com/xTjRWWp.png)

#### 3.1.2 配置主从及其使用说明

1） 简介

该组件支持配置主从分离，即在主库执行新增操作、从库执行其他操作，详细使用方法见下。

2） 配置

- 在配置文件application.properties中，开启主从分离并配置主库和从库（**注意：如果之前配了数据库，需删除之前配置**）。配置如下所示：

		chamc.ds.rw.enable=true
		chamc.ds.rw.master.url=jdbc:mysql://localhost:3306/test?characterEncoding=utf8&useSSL=true
		chamc.ds.rw.master.username=root
		chamc.ds.rw.master.password=1111
		
		chamc.ds.rw.slave.url=jdbc:mysql://10.1.8.147:3306/test?characterEncoding=utf8&useSSL=true
		chamc.ds.rw.slave.username=root
		chamc.ds.rw.slave.password=1111

3） demo

使用之前的demo进行测试，为了看出效果两个数据库未配主从。目前，主库`t_user`表中有4条数据，从库`t_user`表中有1条数据。

![主库](https://i.imgur.com/PRyT0A7.png)  ![从库](https://i.imgur.com/zfRahVM.png)

- 查询所有用户数据，结果为从库数据，如下图：

![](https://i.imgur.com/cAgMwVo.png)

- 新增一个用户，新增到了主库中，如下图：

![](https://i.imgur.com/Fty0FL6.png)
![](https://i.imgur.com/QkjCCIe.png)

- 修改id为1的用户，从库被修改，如下图：

![](https://i.imgur.com/X6uk51B.png)
![](https://i.imgur.com/vnGgnDi.png)

- 删除id为1的用户，`http://localhost:8080/user/1`，从库数据被删除，如下图：

![](https://i.imgur.com/ceOXBYy.png)

#### 3.1.3 配置单点登录及其使用说明

1） 简介

该组件提供域登陆的方法，详细使用方法如下。

2） 配置

- 在application.properties文件中开启security，并配置不需要验证的url和不需要做验证登录的url，例如：

![](https://i.imgur.com/6PB6u4w.png)

- 登录请求分为两种类型：ajax（rest请求，前后端分离）和normal（前后端不分离），配置如下：
 - ajax类型，以档案系统为例，url是ad登陆的服务url，appName为与系统标识名称，retUrl为回调地址。
 
![](https://i.imgur.com/8cbB0IF.png)

 - normal类型，需配置验证成功的跳转地址。

![](https://i.imgur.com/2W5sRPY.png)

- token暂时用不到，先设置为false

![](https://i.imgur.com/mlmmUqe.png)

- 用户信息会放入redis缓存里，所以也要设置redis地址：

![](https://i.imgur.com/FHq5VvY.png)

3） demo

下面以档案系统的域登陆（ajax类型）为例，详细介绍使用方法：

- 注入bean：UserDetailsService

![](https://i.imgur.com/X5GLL9J.png)

- 实现UserDetails

![](https://i.imgur.com/BOQQIKf.png)
![](https://i.imgur.com/7xO72SV.png)

- 实现UserDetailsService

![](https://i.imgur.com/kqtoTVc.png)

- 写一个接口返回ad登录地址

![](https://i.imgur.com/aRqtgpe.png)

- 访问档案系统首页，若系统中不存在用户信息（即用户不处于登录状态），将返回无权限错误提示（状态码为401），前端收到401错误信息后，将向后端请求AD登录地址，前端重定向到AD登录地址（需要带上retUrl），若用户AD登录状态为已登录（电脑已登录域），则带着用户信息重定向到retUrl，否则，弹出登录框，输入用户名、密码进行验证，验证通过则带着用户信息重定向到retUrl。流程图如下：

![](https://i.imgur.com/YcV3rjA.png)

- 前端实现（vue）

![](https://i.imgur.com/qAqJQ6V.png)

![](https://i.imgur.com/eCtDAFF.png)

#### 3.1.4 配置日志打印及其使用说明

1） 简介  

该组件提供对各层（controller、service、repository）进行日志跟踪，详细使用方法如下。

2） 配置

- 在application.properties文件中开启日志跟踪：

    	chamc.tracelog.enable=true

请求某个接口后，如下：

![](https://i.imgur.com/3L5EIt4.png)

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

![](https://i.imgur.com/0Mh31y6.png)

请求一个接口，service的返回值和repository的入参被跟踪打印，如下图：

![](https://i.imgur.com/89GWTy6.png)

### <span id="swagger">3.2 swagger组件</span>

#### 3.2.1 简介
  - 本组件集成了Swagger。Swagger 是一个规范和完整的框架，用于生成、描述、调用和可视化 RESTful 风格的 Web 服务。总体目标是使客户端和文件系统作为服务器以同样的速度来更新。文件的方法，参数和模型紧密集成到服务器端的代码，允许API来始终保持同步。Swagger 让部署管理和使用功能强大的API从未如此简单。了解更多请到[https://swagger.io/](https://swagger.io/)。  
  - 关于Swagger UI官方解释是这样的：*The Swagger UI is an open source project to visually render documentation for a Swagger defined API directly from the API’s Swagger specifcation*。Swagger可以将某种固定格式的JSON数据生成可以视图的在线API文档，支持在线测试，可以清楚的观察到IO数据。
  - 使用本组件可以获得在线API文档，并能在线测试，节省编写接口文档的时间。示例图：

![](https://i.imgur.com/NZ6hqfi.png)

#### 3.2.2 配置

1） 获取swagger组件

同[2.3.4 获取组件](get-web)，将chamc-boot-starter-swagger、chamc-boot-starter-parent下载到本地，并导入。

2） 添加依赖

在pom.xml中的`<dependencies>`标签中添加依赖

	<dependency>
		<groupId>com.chamc.boot</groupId>
		<artifactId>chamc-boot-starter-swagger</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</dependency>

3） 修改配置文件

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

![](https://i.imgur.com/vvAY8Ir.png)

除此界面以外，还提供另一版本，访问`http://localhost:8080/swagger-ui-3.html`，将检索框url改为`/v2/api-docs?group=User`，如图：

![](https://i.imgur.com/aJl57Qf.png)

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

![](https://i.imgur.com/MxfOmOp.png)

![](https://i.imgur.com/iBxy1th.png)

![](https://i.imgur.com/kHxIOrn.png)

详细请参考：[https://github.com/swagger-api/swagger-core/wiki/Annotations#apimodel](https://github.com/swagger-api/swagger-core/wiki/Annotations#apimodel)、[http://www.cnblogs.com/softidea/p/6251249.html](http://www.cnblogs.com/softidea/p/6251249.html)


### <span id="bpm">3.3 工作流组件</span>

详情请参考网盘文件[http://hq-spsdocument/_layouts/15/DocIdRedir.aspx?ID=C2A742TNNUZA-1797567310-1214](http://hq-spsdocument/_layouts/15/DocIdRedir.aspx?ID=C2A742TNNUZA-1797567310-1214)

或查看git项目下docs -> 开发平台后端框架参考指南-流程引擎模块.md；

## <span id="how-to">4 “How-to”指南</span>

## 5 附录



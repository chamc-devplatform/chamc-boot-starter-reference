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

### 2.1 开发平台后端框架介绍

1. 简介    
开发平台后端框架基于Spring实现，只需引入依赖便可使用。
2. 系统要求  
开发平台后端框架使用的Spring Boot的版本为：Spring Boot 1.5.4.RELEASE；JDK版本为：jdk1.8；MAVEN版本为：maven3.5.0。

### 2.2 环境安装

使用开发平台后端框架需要以下环境：  

- [jdk1.8](#jdk)
- [maven3.5.0](#maven)
- [spring tool suite](#sts)  

以上软件都可通过 [公司网盘](http://hq-spsdocument/Documents/Forms/AllItems.aspx?RootFolder=%2FDocuments%2F4-%E8%BD%AF%E4%BB%B6%E5%BC%80%E5%8F%91%E9%83%A8%2F%E5%9F%B9%E8%AE%AD%2F171013-SpringMVC%E5%92%8CJPA%E5%9F%BA%E7%A1%80-%E7%BD%97%E6%98%8E%E5%BC%BA%2F%E8%BD%AF%E4%BB%B6) 获得。

#### <span id="jdk">2.2.1 jdk的安装配置</span>

##### 1. 安装jdk
根据安装提示进行安装。**注意：安装路径不要包含中文、空格、特殊字符** 
##### 2. 配置环境变量  

1) 新增环境变量JAVA_HOME 
<pre>例：C:\Program Files\Java\jdk1.8.0_131</pre> 
  
![](https://i.imgur.com/49uVnkH.png)  

2) 新增环境变量path(最好将其放在第一位)  
<pre>例：C:\Program Files\Java\jdk1.8.0_131\bin;C:\Program Files\Java\jdk1.8.0_131\jre\bin;</pre>
 
![](https://i.imgur.com/d3H1T7E.png)

3） 新增环境变量CLASSPATH  
<pre>例：.;%JAVA_HOME%\lib;%JAVA_HOME%\lib\tools.jar</pre>

![](https://i.imgur.com/xd78npM.png)

##### 3. 在cmd中执行java -version,若出现下图则安装成功  

![](https://i.imgur.com/k5g3ZUa.png)

#### <span id="maven">2.2.2 maven的安装配置</span>

##### 1. 解压apache-maven-3.5.0-bin.zip到某个目录（例如D盘） **注意：解压路径不要包含中文、空格、特殊字符**  
##### 2. 设置环境变量  
1） 新建系统变量  MAVEN_HOME  变量值：D:\apache-maven-3.5.0  

![](https://i.imgur.com/KcBDyNb.png)

2） 编辑系统变量  Path  添加变量值：;%MAVEN_HOME%\bin  

![](https://i.imgur.com/hiaMCLH.png)

##### 3. 打开命令行，输入 mvn --version，若出现以下情况则配置成功。 

![](https://i.imgur.com/yY62UDa.png)

##### 4. 在maven目录下的conf文件夹的settings.xml中加入：  
  
1） 在`<proxies>`标签中加入代理：  

	<proxy>  
		<id>optional</id>  
		<active>true</active>  
		<protocol>http</protocol>  
		<username></username>  
		<password></password>  
		<host>10.80.2.4</host>  
		<port>80</port>  
		<nonProxyHosts>local.net|some.host.com</nonProxyHosts>  
	</proxy>

2） 在`<profiles>`标签中加入：  

	<profile>  
		<id>jdk-1.8</id>  
		<activation>  
			<activeByDefault>true</activeByDefault>  
			<jdk>1.8</jdk>  
		</activation>  
		<properties>  
			<maven.compiler.source>1.8</maven.compiler.source>  
			<maven.compiler.target>1.8</maven.compiler.target>  
			<maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>  
		</properties>  
	</profile>  
  
3） 在`<activeProfiles>`标签中添加：  

   `<activeProfile>jdk-1.8</activeProfile>`
		
#### <span id="sts">2.2.3 spring tool suite的安装配置</span>

##### 1. STS的安装

1） 解压 spring-tool-suite-3.8.4.RELEASE-e4.6.3-win32-x86_64.zip （**注意：解压路径不要包含中文、空格、特殊字符**）  
2） 打开 sts-3.8.4.RELEASE 的 STS.exe ，即可使用  
  
##### 2. STS的配置

1） Maven配置  
  
- 打开STS，进入windows —》Preferences —》Maven —》Installations。  
- 点击Add...选择Maven的所在目录。  
 
![1](https://i.imgur.com/ilpB3Wu.png)  

- 勾选这个Maven，并点击Apply。    

![2](https://i.imgur.com/oMYGNrY.png)  

- 选择User Settings，将设置User Settings为自己的settings.xml，OK。  
		
![3](https://i.imgur.com/1quxhE5.png)  

2） JDK配置

- 打开STS，进入windows —》Preferences —》Java —》Installed JREs。  

![4](https://i.imgur.com/MZgsUFu.png)  

- 选中jdk，选择Edit...。将jdk改为指向jdk的目录（**而不是jre**），Finish。 

![5](https://i.imgur.com/W8hkZp6.png)

3） <span id="daili">配置代理（可不配）</span>  

- 打开 windows —》Preferences —》Network Connections，配置如图所示。

![](https://i.imgur.com/UktUnq1.png)

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

1） 申请svn库的开发平台项目权限，地址：`https://10.1.8.112/svn/DevelopPlatform`，将chamc-boot-starter-web项目下载到本地。

![](https://i.imgur.com/pE7WnsV.png)

2） 将项目导入工作空间，maven -》 update project...

![](https://i.imgur.com/kudFdb2.png)

### <span id="querydsl">2.4 关于数据库操作的一些介绍</span>

#### 2.4.1 使用JPA

1） 示例请见，[2.3.1 — 4 — 9）](#jpa)  
2） JPA学习资料请参考[Spring Data JPA - Reference Documentation](https://docs.spring.io/spring-data/jpa/docs/1.11.6.RELEASE/reference/html/#jpa.query-methods.query-creation) 

#### 2.4.2 使用querydsl

1） 示例：查询年龄小于X岁的用户并按年龄倒序排列

	public List<User> processAgeLtBusinessByQsdl(Long age){
		AbstractJPAQuery<User, JPAQuery<User>> query = this.repository.createDslQuery();
		QUser qUser = QUser.user;
		JPQLQuery<User> jpqlQuery = query.select(qUser).from(qUser).where(qUser.detail.age.lt(age)).orderBy(qUser.detail.age.desc());
		return jpqlQuery.fetch();
	}

2） querydsl学习资料请参考[Querydsl Reference Guide](http://www.querydsl.com/static/querydsl/4.1.3/reference/html_single/)

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
- 1. 简介
- 2. 配置
- 3. demo

#### 3.1.4 配置日志打印及其使用说明

1） 简介  

改组件提供对各层（controller、service、repository）进行日志跟踪，详细使用方法如下。

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

#### <span id="bpm_1">3.3.1 流程引擎基本信息</spam>

- 术语

> - 流程定义：业务过程的形式化描述，过程可分解为一系列的子过程和活动，其中定义包括描述过程起始、终止的活动关系网络，以及一些关于个体行为的信息，具体而言，即构成过程的活动以及各活动的关系、组织成员的角色、应用中的数据结构等。
> - 活动（activity）：流程定义中的活动，定义了在业务过程中某个环节的信息
> - 流程实例：一个工作流过程的具体执行。
> - 任务：对应流程定义中的活动，是具体的执行实例
> - BPM：业务流程管理(Business Process Management)
> - BPM模块：开发平台建设的流程相关子项目模块，项目名“chamc-boot-starter-bpm”

- 介绍

>流程引擎模块属于开发平台的一个子模块，专门为业务系统提供工作流及业务自动化服务。该模块能够以maven依赖的方式快速引入在建项目中，对于基础的流程发起、待办已办任务查询等操作，直接调用相应的rest接口即可。在提供流程处理基础服务 + 租户、角色等基础管理服务的基础上，项目支持在管理页面设计流程图、在管理后台操作流程。希望通过流程引擎模块，为应用及项目的开发提供支持。


- SDK接口说明

>项目提供通用的`controller`层接口访问，能够最大程度的便捷开发。同时项目还支持开发者以SDK的形式开发，SDK组件封装了流程引擎的各种服务，以方便各个系统的个性化需求。

#### <span id="bpm_2">3.3.2 集成及获取服务</span>

3.3.2.1 集成方式

流程引擎以jar工具包的形式提供服务，提供以下两种形式的集成：

- maven项目的集成：  

如果能够连接公司私服，直接在pom文件添加依赖即可，可以跳过**Step 1**，否则需要先获取jar包，如下所述。

**Step 1**：在**SVN**获取"chamc-boot-starter-bpm"项目，导入STS。

**Step 2**：在pom文件的`<dependencies></dependencies>`中添加以下信息

	<dependency>
		<groupId>com.chamc.boot</groupId>
		<artifactId>chamc-boot-starter-bpm</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</dependency>

**Step 3**：在工程目录上右键，选择 `Maven` -> `Update Project` -> `OK`，项目自动编译后，如果没有异常，就能够使用流程服务了。

#### <span id="bpm_3">3.3.3 第一个流程</span>

- 步骤概述：

> [在管理页面设计流程图（包括流程参与人、操作等的配置）](#bpm_3_1)  
> [在开发项目中引入`starter-bpm`模块](#bpm_3_2)  
> [项目中的流程配置](#bpm_3_3)  
> [流程发起及操作](#bpm_3_4)

- Step 1：<span id="bpm_3_1">在管理页面设计流程图（包括流程参与人、操作等的配置）</span>

1）访问并登录**流程引擎后台管理平台**（初始用户名密码：`administrator`+`111111`），登录后主页面及导航栏如图process-1所示，`系统设置`的子菜单可以管理用户、机构和角色等，可以分别点击进行对应的操作。为了方便使用，系统初始已预置初始数据，可查看确认。

![图 process-1：系统设置](https://i.imgur.com/dxw3Jx4.png)

<center>图 process-1：系统设置</center>

2）点击`工作流管理`-`流程创建管理`，如图process-2所示，进入流程设计管理页面，在该页面可以完成流程的新建、发布等。

![图 process-2：流程设计界面](https://i.imgur.com/2iqIYgv.png)

<center>图 process-2：流程设计界面</center>

3）点击新建流程，如图process-3所示，填写流程定义信息（所属租户，流程key等），填写完成后，点击确认保存流程定义信息。再次点击主页面的导航栏`工作流管理`-`流程创建管理`,可以看到列表第一条数据为刚才新建的流程，点击编辑操作，进入流程设计界面，如图process-4所示。

![图 process-3：新建流程](https://i.imgur.com/WoDqP2d.png)

<center>图 process-3：新建流程</center>

![图 process-4：进入流程设计界面](https://i.imgur.com/2xZUH6z.png)

<center>图 process-4：进入流程设计界面</center>

4）设计流程图，在demo示例中设计简单的流程（启动-制单-领导审批-结束）。点击左侧`启动事件`，将`事件`控件拖入页面中间设计区域，如图process-5所示；点击拖入的`事件`，周围显示可以连接的其他控件，点击`分配给特定人的任务`，如图process-6所示，添加一个新的activity；按照同样的步骤，点击刚新建的activity，在周围出现的选项中点击`分配给特定人的任务`，添加第二个activity；同理点击第二个activity，选择红色的圈（一个无触发器的结束任务），添加结束节点。

![图 process-5：拖入启动事件](https://i.imgur.com/5Nn8DQ3.png)

<center>图 process-5：拖入启动事件</center>

![图 process-6：添加activity](https://i.imgur.com/Qlx6Iyv.png)

<center>图 process-6：添加activity</center>

5）配置activity。如图process-7所示，点击后可以在右侧菜单设置activity属性，点击创建的第一个activity，输入id、名称，点击代理后的输入框，选择参与人信息，如图process-8所示，确认后点击任务节点功能，配置对于该activity的功能操作，勾选下一步操作，保存确认后，点击流程设计页面中控件栏上方的保存按钮，并关闭该页面，如图process-9所示。

![图 process-7：修改属性](https://i.imgur.com/Ixi3we6.png)

<center>图 process-7：修改属性</center>

![图 process-8：设置参与人](https://i.imgur.com/3iyabyT.png)

<center>图 process-8：设置参与人</center>

![图 process-9：保存](https://i.imgur.com/91F6Xfp.png)

<center>图 process-9：保存</center>

6）发布流程图，再次点击主页面的导航栏`工作流管理`-`流程创建管理`，找到刚编辑的流程定义的条目并点击发布，提示流程发布成功，即完成了流程的设计与发布过程。

- Step 2：<span id="bpm_3_2">在开发项目中引入`bpm`模块</span>

1）参考[3.3.2 集成及获取服务](#bpm_2)，为项目添加bpm模块依赖。

- Step 3：<span id="bpm_3_3">项目中的流程配置</span>

以SpringBoot项目为例：

1）在配置文件（application.properties）中添加如下所示的内容：

    chamc.bpm.type=tansun
    chamc.bpm.tansun.url=http://localhost:8080/workflow-console #流程引擎服务地址
    chamc.bpm.tansun.tenant-id=archive  #租户ID，可以在“流程引擎后台管理平台——工作流管理——租户管理”查看
    chamc.bpm.tansun.default-classify-code=root #流程分配编码

- Step 4：<span id="bpm_3_4">流程发起及操作</span>

1）发起流程

在SpringBoot项目需要的地方注入`com.chamc.boot.bpm.service.IInstanceService`，调用iInstanceService.startBpm(startBpmParam)即可启动流程，示例：

    import org.springframework.beans.factory.annotation.Autowired;

    import com.chamc.boot.bpm.model.process.param.StartBpmParam;
    import com.chamc.boot.bpm.service.IInstanceService;

    public class InstanceServiceDemo {

    	private @Autowired IInstanceService iInstanceService;
    
    	public void startBpm() {
    		StartBpmParam startBpmParam = new StartBpmParam();
    		startBpmParam.setProcessKey("process-helloworld");
    		startBpmParam.setUserId("user1");
    		startBpmParam.setTitle("测试任务标题");
    		iInstanceService.startBpm(startBpmParam);
    	}
    }

2）查看待办**/**已办任务

注入`com.chamc.boot.bpm.service.ITaskService`，调用iTaskService.queryTodo(userId, pageable)方法查看待办任务列表，根据[流程定义信息](#bpm_3_1)，此时第一个activity配置的参与人应收到一条待办任务。或者通过queryDone接口查看已办任务列表，如下所示：

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.data.domain.Page;
    import org.springframework.data.domain.PageRequest;
    import org.springframework.data.domain.Pageable;
    
    import com.chamc.boot.bpm.model.task.TaskDone;
    import com.chamc.boot.bpm.model.task.TaskTodo;
    import com.chamc.boot.bpm.service.ITaskService;
    
    public class TaskServiceDemo {
    
    	private @Autowired ITaskService iTaskService;
    	
    	public Page<TaskTodo> queryTaskTodo(String userId) {
    		Pageable pageable = new PageRequest(0, 20);		//构造pageable对象，查询第1页数据，分页大小为20
    		Page<TaskTodo> todoPage = iTaskService.queryTodo(userId, pageable);
    		return todoPage;
    	}
    	
    	public Page<TaskDone> queryTaskDone(String userId) {
    		Pageable pageable = new PageRequest(0, 20);
    		Page<TaskDone> donePage = iTaskService.queryDone(userId, pageable);
    		return donePage;
    	}
    }

待办任务TaskTodo列表如下所示，包含分页信息及待办任务列表，对每个待办任务，包含该任务当前所处的环节、基本信息、可进行的操作和流程实例信息等，其中基本信息中的`pcUrl`和`mobileUrl`分别对应pc段和移动端的任务详情页，该地址可以通过配置文件配置。获取待办列表后直接访问待办的pcUrl/mobileUrl即可进入任务的详情页面。

在任务详情页对待办任务进行操作，操作的按钮可以从待办任务的operations中获取，operations为对象数组，Operation返回值对象，包含信息较多：1、返回对应activity可进行的操作、操作名称及url；2、通过isNeedUserId标识该操作是否需要传用户id；3、通过isPlural标识需要用户id的话，是传1个还是多个；4、如果传的用户id（即下一个activity要用的用户id）有限定范围，那么会通过userRange返回给用户。operation对象示例可参考[Operation](#Operation)。

对于前端使用来说，点击按钮时，取值Operation中的url，并按照Operation的要求附加参数，使用示例参考[actionTask](#actionTask)，

	{
	  "content": [
	    {
	      "activity": {
	        "first": true,
	        "id": "string",
	        "last": true,
	        "name": "string"
	      },
	      "createTime": "2017-12-18T05:23:44.730Z",
	      "dueDate": "2017-12-18T05:23:44.730Z",
	      "executionId": "string",
	      "id": "string",
	      "mobileUrl": "string",
	      "name": "string",
	      "operations": [
	        {
	          "needUserId": true,
	          "op": "同意",
	          "plural": true,
	          "text": "string",
	          "url": "string",
	          "userRange": [
	            "string"
	          ]
	        }
	      ],
	      "pcUrl": "string",
	      "processInstance": {
	        "businessKey": "string",
	        "currentActivity": {
	          "first": true,
	          "id": "string",
	          "last": true,
	          "name": "string"
	        },
	        "definitionId": "string",
	        "definitionName": "string",
	        "id": "string",
	        "startParticipant": {
	          "code": "string",
	          "id": "string",
	          "name": "string",
	          "org": "string",
	          "orgName": "string",
	          "type": "USER"
	        },
	        "title": "string"
	      }
	    }
	  ],
	  "first": true,
	  "last": true,
	  "number": 0,
	  "numberOfElements": 0,
	  "size": 0,
	  "sort": {},
	  "totalElements": 0,
	  "totalPages": 0
	}

<span id="actionTask">前端任务操作示例：actionTask</span>

	function actionTask(instanceId, taskId, url, needUserId, plural) {
		if(needUserId) {
			//弹框选人
		}
		var params = {};
		params.instanceId = instanceId;
		params.taskId = taskId;
		params.operatorId = "";
		params.comment = $("#task_comment").val();
		params.variables = {};
		if(needUserId) {
			//添加用户变量，多个用逗号隔开
			params.userId = "default_user_id";
		}
		$.post(url, params, function(result) {
			if("OK" == result) {
				document.location = "http://localhost:8000/zouzhentian@chamc.com.cn@8499/todo";
			} else {
				alert(result);
			}
		});
	}

3）处理任务

`bpm`模块提供了任务的基本操作服务，直接访问请求即可。在第一个流程中，我们通过这种方式操作任务，例如启动应用后，根据应用部署的ip:port，直接访问{http://ip:port}/bpm/task/agree，就能完成该任务的同意操作。  
操作和驳回的请求路径及部分参数说明如下所示，完整说明信息请参考[3.3.6 BPM模块基本请求](#bpm_6)。

|----|----|----|
|请求说明|URL|参数|
|同意|/bpm/task/agree|operatorId, taskId|
|驳回上一步|/bpm/task/reject|operatorId, taskId|

4）查看流转明细

    private @Autowired IInstanceService iInstanceService;

	public List<ProcessTransferDetail> queryTransferDetail(String instanceId) {
		List<ProcessTransferDetail> result = iInstanceService.queryTransferDetail(instanceId);
		return result;
	}

至此，已经完成了第一个流程demo。包括流程设计、流程发起、待办已办查询、任务处理及查询流转明细等。

#### <span id="bpm_4">3.3.4 流程设计与管理平台</span>

#### <span id="bpm_5">3.3.5 流程服务说明</span>

1）流程服务总述：  
流程服务模块（BPM模块）以jar包的形式添加到项目系统中，为业务系统提供流程相关的所有服务。在开发的过程中只需要按照[3.3.2 集成及获取服务](#bpm_2)为项目添加流程引擎模块依赖，并在项目配置文件中添加一些简单的信息，即可使用流程引擎服务。

BPM模块能够提供的流程引擎服务包括流程设计、流程发起、待办已办查询、任务处理及查询流转明细，并包括查询activity定义、查询流程变量和查询任务可进行的操作等。同时支持直接以请求的方式调用和SDK接口的形式调用两种方式。开发人员在使用流程服务时，基础使用可以直接调用bpm封装好的请求即可，bpm提供的请求详情可参考[3.3.6 BPM模块基本请求](#bpm_6)，如果需要其他更加丰富的功能，注入相应的service并调用接口即可，接口的入参和出参详情可参考[3.3.7 SDK接口](#bpm_7)。

BPM模块使用前需要在项目的`application.properties`中配置流程信息，分为必须信息和可选信息两种，配置的具体信息可参考[【示例】application中配置流程信息](#properties)

如果流程中涉及到会签的使用场景，可以在设计流程图的时候进行配置，具体可参考[【示例】会签使用场景](#huiqian)

2）<span id="properties">【示例】application中配置流程信息</span>

通过配置信息，能够对指定processKey的流程的key、全局的待办/已办的pcUrl/mobileUrl进行配置，也能够对该key对应的每个activity的属性进行配置，包括操作按钮的名称、是否第一个/最后一个activity以及单个activity的url。

配置项以`chamc.bpm.processes`开头，后跟流程定义别名（只供配置信息用），以下示例中为`zbwsdazl`。

针对流程的属性有`processKey、todo-pc-url、done-pc-url、todo-mobile-url、done-mobile-url`，url会在查询任务详情/任务列表时返回。配置流程属性时以`chamc.bpm.processes.流程别名.`开头

针对流程中单个activity的属性有`todo-pc-url、done-pc-url、todo-mobile-url、done-mobile-url、first、last`，其中first、last为boolean型。在配置activity属性时，以`chamc.bpm.processes.流程别名.activity的Id.`开头，activityId可以在流程设计（画流程图）的时候设置。

    chamc.bpm.type=tansun   #流程引擎类型
    chamc.bpm.tansun.url=http://10.1.1.177:8081/workflow-console    #流程引擎服务url
    chamc.bpm.tansun.tenant-id=archive  #租户id
    chamc.bpm.tansun.default-classify-code=root #流程分类
    
    chamc.bpm.processes.zbwsdazl.process-key=zbwsdazl_process   #以zbwsdazl为标识配置processKey为zbwsdazl_process的流程
    chamc.bpm.processes.zbwsdazl.activities.gdys.operations.agree.text=同意审批     #zbwsdazl流程中id为gdys的activity的同意按钮的名称自定义 【注：每个activity的每个按钮的名称都可以自定义，按钮类型列表参考3.3.7.x【示例】按钮列表】
    chamc.bpm.processes.zbwsdazl.activities.gdys.operations.reject.text=驳回  #id为gdys的activity的驳回按钮的名称自定义
    chamc.bpm.processes.zbwsdazl.activities.tjsh.first=true #配置id为tjsh的activity为第一个活动，查询任务或任务列表时返回
    chamc.bpm.processes.zbwsdazl.activities.wcgd.last=true  #配置id为wcgd的activity为最后一个活动
    chamc.bpm.processes.zbwsdazl.todo-pc-url=receiveAudit   #配置以zbwsdazl为标识的流程的全局待办任务的pc-url
    chamc.bpm.processes.zbwsdazl.todo-mobile-url=receiveAudit   #配置以zbwsdazl为标识的流程的全局待办任务的mobile-url
    chamc.bpm.processes.zbwsdazl.done-pc-url=receiveAudit
    chamc.bpm.processes.zbwsdazl.activities.tjsh.todo-pc-url=submitReview   #zbwsdazl中id为tjsh的activity的待办任务的pc-url地址
    
    chamc.bpm.tansun.log-level=full     #可选项为NONE、BASIC、HEADERS、FULL，流程引擎的日志级别配置

3）<span id="huiqian">【示例】会签使用场景</span>

（待补充）

#### <span id="bpm_6">3.3.6 BPM模块基本请求</span>

- com.chamc.boot.bpm.controller.TaskController

##### <span id="bpm_6_1">1）说明</span>

基本请求是在配置好BPM依赖之后，可以直接通过restful发起的请求，不需要再业务系统中书写其他代码。如果需要在操作前/后添加业务逻辑处理时，有以下两个方案：

a、针对每个流程新建listener类并实现`com.chamc.boot.bpm.controller.listener.BpmOperateListener`接口即可，须实现该接口的`String processKey();`方法，并且在`before`方法中需要setOperatorId（操作人用户id），如下所示；

b、BpmOperateListener中有`before、after、afterThrowing`三个时间段的方法，如果只需要其中一个或者几个的话，可以新建listener类并继承`com.chamc.boot.bpm.controller.listener.support.BpmOperateAdapter`即可，重载的方法同BpmOperateListener中所述方法。

    import com.chamc.boot.bpm.controller.listener.BpmOperateListener;
    import com.chamc.boot.bpm.controller.param.BpmOperateParam;
    import com.chamc.boot.bpm.model.common.OperationType;
    
    public class AdminArchivingAuditListener implements BpmOperateListener {
    	
    	private final static String processKey = "zbwsdazl_process";
    
    	@Override
    	public void before(BpmOperateParam param, OperationType type) {
            //操作执行前，设置操作人
    		param.setOperatorId(UserUtil.getUserId());
            //***
    	}
    
    	@Override
    	public void after(BpmOperateParam param, OperationType type) { }
    
    	@Override
    	public void afterThrowing(BpmOperateParam param, OperationType type, Throwable t) { }
    
    	@Override
    	public String processKey() {
    		return processKey;
    	}
    }

##### <span id="bpm_6_2">2）基本请求列表</span>

|----|----|----|----|----|
|描述|请求url|请求类型|入参|出参|
|同意审批|/bpm/task/agree|Post(Json)|BpmOperateParam|String(0)|
|驳回到上一步|/bpm/task/reject|Post(Json)|BpmOperateParam|String(0)|
|驳回到第一步|/bpm/task/rejectToFirst|Post(Json)|BpmOperateParam|String(0)|
|改派|/bpm/task/delegate|Post(Json)|BpmOperateParam|String(0)|
|加签|/bpm/task/addSign|Post(Json)|BpmOperateParam|String(0)|
|指派|/bpm/task/assign|Post(Json)|BpmOperateParam|String(0)|

##### <span id="bpm_6_3">3）异常说明</span>

当请求处理发生异常时，统一抛出`BpmException`，错误信息内容包含在异常中。

#### <span id="bpm_7">3.3.7 SDK接口</span>

##### <span id="bpm_7_1"> 1）说明</span>

SDK接口能够让开发人员快速的开发应用，进行灵活的流程应用的支持。在SDK中封装了工作流引擎提供的各种服务，为开发人员提供便利。

##### <span id="bpm_7_2">2）获取接口的方式</span>

目前SDK主要提供2类的接口，包路径为`com.chamc.boot.bpm.service`，接口分类列表如下：

|----|----|----|
|编号|interface|描述|
|1|IInstanceService|流程实例相关的service，包括流程启动、终止以及根据流程实例/定义的一些查询方法|
|2|ITaskService|任务相关service，包括查询待办、已办，以及操作任务等|

##### <span id="bpm_7_3">3）接口列表</span>

**IInstanceService**

|---|----|----|----|----|
|编号|方法|入参|出参|描述|
|1|startBpm|StartBpmParam|~|启动流程，启动参数中有不同情况的处理|
|2|terminateBpm|userId，instanceId|~|终止流程|
|3|terminateBpm|userId，instanceId，comment|~|终止流程，可传评论参数|
|4|queryTransferDetail|instanceId|`List<ProcessTransferDetail>`|获取流转明细|
|5|getProcessVariables|instanceId|`List<Variable>`|获取流程变量|
|6|queryProcessInstance|processKey|`List<ProcessInstance>`|获取指定流程key对应的运行中的流程实例|

**ITaskService**

|---|----|----|----|----|
|编号|方法|入参|出参|描述|
|1|queryTodo|userId,pageable|`Page<TaskTodo>`|分页查询待办列表|
|2|getTaskTodo|taskId|`TaskTodo`|按照taskId查询待办任务|
|3|queryDone|userId,pageable|`Page<TaskDone>`|分页查询已办列表|
|4|getTaskDone|taskId|`TaskDone`|根据taskId查询已办任务|
|5|completeTask|taskId, userId|~|同意审批/下一步|
|5|completeTask|taskId, userId, variableList|~|~|
|5|completeTask|taskId, userId, comment|~|~|
|5|completeTask|taskId, userId, comment, variableList|~|同意审批/下一步，可选参数List<Variable>，传流程变量列表|
|5|completeTask|taskId, userId, nextOrgId, comment, variableList|~|同意审批/下一步，可指定下一个activity参与人的orgId|
|6|completeAndAssign|taskId, userId, nextParticipateList|~|同意审批并指派下一步的参与人（List<Participant>）|
|6|completeAndAssign|taskId, userId, comment, nextParticipateList|~|同意审批并指派下一步的参与人|
|7|addSign|taskId, userIdList|~|加签|
|7|addSign|taskId, userIdList, comment|~|~|
|8|reject|taskId, userId|~|驳回到上一步|
|8|reject|taskId, userId, variableList|~|附件参数：变量列表|
|8|reject|taskId, userId, comment|~|驳回到上一步|
|8|reject|taskId, userId, comment, variableList|~|附件参数：变量列表|
|9|rejectToFirst|taskId, userId|~|驳回到制单人（第一个activity）|
|9|rejectToFirst|taskId, userId, variableList|~|~|
|9|rejectToFirst|taskId, userId, comment|~|~|
|9|rejectToFirst|taskId, userId, comment, variableList|~|~|
|10|delegate|taskId, userId, toUserId|~|转办/委派|
|10|delegate|taskId, userId, toUserId, comment|~|~|

##### <span id="bpm_7_4">4) Model说明</span>

- [a、BpmOperateParam](#BpmOperateParam)
- [b、Variable](#Variable)
- [c、AssignInfo](#AssignInfo)
- [d、Activity](#Activity)
- [e、Comment](#Comment)
- [f、UserType（枚举）](#UserType)
- [g、Participant](#Participant)
- [h、StartBpmParam](#StartBpmParam)
- [i、ProcessInstance](#ProcessInstance)
- [j、Operation](#Operation)
- [k、ProcessTransferDetail](#ProcessTransferDetail)
- [l、TaskDone](#TaskDone)
- [m、TaskTodo](#TaskTodo)
- [n、OperationType（枚举）](#OperationType)

**<span id="BpmOperateParam">a、BpmOperateParam</span>**

    {
      "comment": "string",  //审批意见
      "definitionId": "string", //流程定义id
      "instanceId": "string",   //流程实例ID
      "operatorId": "string",   //任务操作人Id（必须）
      "taskId": "string",       //任务Id
      "userIds": [              //需要用户信息时添加，比如指派/加签
        "string"
      ],
      "variables": [            //变量
        {
          "name": "string",
          "type": "string",     //变量类型，如string/boolean/integer等
          "value": "string"
        }
      ]
    }

**<span id="Variable">b、Variable</span>**

    {
        "name": "string",
        "type": "string",     //变量类型，如string/boolean/integer等
        "value": "string"
    }

**<span id="AssignInfo">c、AssignInfo</span>**

    {
        "userIds": "String[]", //被指派的用户id数组
    }

**<span id="Activity">d、Activity</span>**

    {
      "id": "string",
      "name": "string",
      "first": "boolean",   //是否流程的第一个activity
      "last": "boolean",   //是否流程的最后一个activity
    }

**<span id="Comment">e、Comment</span>**

    {
      "id": "string",
      "author": "string", //评论人
      "message": "string",
      "createTime": "Date"
    }

**<span id="UserType">f、UserType（枚举）</span>**

    {
        USER,
        DEPT,
        ORG,
        USER_GROUP
    }

**<span id="Participant">g、Participant</span>**

    {
      "id": "string",  //用户id
      "code": "string", //用户表尼玛
      "name": "string",   //用户名称
      "org": "string",   //用户所在机构Id
      "orgName": "string",   //用户所在机构名称
      "type": "UserType" //用户类型
    }

**<span id="StartBpmParam">h、StartBpmParam</span>**

    {
      "userId": "string",
      "processKey": "string", //流程定义key
      "bussinessKey": "string",   //业务单号
      "title": "string",   //流程实例标题
      "orgId": "string",       //制单人机构ID
      "toNext": "boolean",      //是否跳过第一个activity
      "variables": "List<Variable>",  //变量
      "assignInfo": "AssignInfo"        //启动时的指派信息
    }

**<span id="ProcessInstance">i、ProcessInstance</span>**

    {
      "id": "string",  //流程实例id
      "title": "string", //流程实例名称
      "businessKey": "string",
      "definitionId": "string",       //流程定义id，包括版本信息等
      "definitionName": "string",       //流程定义名称
      "currentActivity": "Activity",   //当前所在节点
      "startParticipant": "Participant",      //启动人信息
    }

**<span id="Operation">j、Operation</span>**

    {
        "op", "OperationType",
        "text", "String",
        "isPlural": "boolean",
        "url", "String",
        "isNeedUserId": "boolean",
        "userRange", "List<String>",
    }

**<span id="ProcessTransferDetail">k、ProcessTransferDetail</span>**

    {
      "id": "string",  //任务id
      "name": "string", //任务节点名称
      "executionId": "string",       //任务执行id
      "createTime": "Date",   //创建时间
      "startTime": "Date",   //创建时间
      "endTime": "Date",
      "durationInMillis": "Long", //持续时间
      "dueDate": "Date",   //持续时间
      "deleteReason": "string",       //待办删除原因，比如处理了，驳回了，终止了，转办了等
      "processInstance": "ProcessInstance",//流程实例
      "participant": "Participant",      //任务参与人信息
      "comment": "Comment"   //评论   
    }

**<span id="TaskDone">l、TaskDone</span>**

    {
      "id": "string",  //任务id
      "name": "string", //任务节点名称
      "executionId": "string",       //任务执行id
      "startTime": "Date",   //创建时间
      "endTime": "Date",
      "durationInMillis": "Long", //持续时间
      "dueDate": "Date",   //持续时间
      "deleteReason": "string",       //待办删除原因，比如处理了，驳回了，终止了，转办了等
      "processInstance": "ProcessInstance",//流程实例
      "assigneeParticipant": "Participant",      //任务参与人信息
      "comment": "Comment",   //评论
      "pcUrl": "string",
      "mobileUrl": "string"       //移动审批Url    
    }

**<span id="TaskTodo">m、TaskTodo</span>**

    {
      "id": "string",  //任务id
      "name": "string", //任务节点名称
      "createTime": "Date",   //创建时间
      "dueDate": "Date",   //持续时间
      "executionId": "string",       //任务执行id
      "processInstance": "ProcessInstance",//流程实例
      "operations": "List<Operation>",      //任务可执行的操作
      "activity": "Activity",   //任务对应节点的描述对象
      "pcUrl": "string",
      "mobileUrl": "string"       //移动审批Url    
    }

**<span id="OperationType">n、OperationType（枚举）</span>**

|----|----|----|----|
|按钮名称(默认名称)|code|流程设计图对应任务节点功能设置|描述|
|同意|agree|下一步|同意审批|
|拒绝|refuse||
|驳回|reject|退回上一步|
|驳回到第一步|rejectToFirst|退回第一步|
|驳回到指定环节|rejectToSpecified|退回指定节点|
|改派|delegate|转发|
|加签|addSign|加签|
|指派|assign||
|终止|terminate|终止流程|
|挂起|suspend|挂起流程|
|激活|activate|激活流程|
|减签|delSign|减签|

#### <span id="bpm_8">3.3.8 错误说明</span>

|----|----|----|
|错误码|错误提示|描述|
|**500**|查询待办发生异常|流程引擎内部错误，可能原因为：1、参数错误，2、流程引擎bug|

#### <span id="bpm_9">3.3.9 同步模块说明</span>

#### <span id="bpm_10">3.3.10 版本变更历史</span>

|----|----|----|
|SDK版本|发布时间|更新内容描述|
|0.0.1SNAPSHOT||新建项目|

## <span id="how-to">4 “How-to”指南</span>

## 6 附录



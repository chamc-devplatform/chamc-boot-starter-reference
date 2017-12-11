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

##### 3. 添加依赖

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

##### 4. 开始编码

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

##### <span id="get-web">5. 获取组件</span>

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
- 1. 简介
- 2. 配置
- 3. demo

#### 3.1.3 配置单点登录及其使用说明
- 1. 简介
- 2. 配置
- 3. demo

#### 3.1.4 配置日志打印及其使用说明
- 1. 简介
- 2. 配置
- 3. demo

### <span id="swagger">3.2 swagger组件</span>

- 1. 简介
- 2. 配置
- 3. demo

### <span id="bpm">3.3 工作流组件</span>

#### <span id="bpm_1">3.3.1 流程引擎基本信息</spam>

- 术语

>  

- 介绍

>流程引擎模块属于开发平台的一个子模块，专门为业务系统提供工作流及业务自动化服务。该模块能够以maven依赖的方式快速引入在建项目中，对于基础的流程发起、待办已办任务查询等操作，直接调用相应的rest接口即可。在提供流程处理基础服务 + 租户、角色等基础管理服务的基础上，项目支持在管理页面设计流程图、在管理后台操作流程。希望通过流程引擎模块，为应用及项目的开发提供支持。

- SDK接口说明

>项目提供通用的`controller`层接口访问，能够最大程度的便捷开发。同时项目还支持开发者以SDK的形式开发，SDK组件封装了流程引擎的各种服务，以方便各个系统的个性化需求。

#### <span id="bpm_2">3.3.2 集成及获取服务</span>

3.3.2.1 集成方式

流程引擎以jar工具包的形式提供服务，提供以下两种形式的集成：

- maven项目的集成：  

如果能够连接公司私服，直接在pom文件添加依赖即可，可以跳过**Step 1**，否则需要先获取jar包，如下所述。

**Step 1**：在**SVN**获取jar包，下载到本地后，在cmd命令行模式执行如下命令，将jar包安装到本地maven仓库，执行命令时确认路径、文件名及版本信息的正确性。

    mvn install:install-file -Dfile=下载的jar包的路径 -DgroupId=com.chamc.boot -DartifactId=chamc-boot-starter-bpm -Dversion=0.0.1-SNAPSHOT -Dpackaging=jar

注：如果cmd命令行提示命令不存在的问题，请检查maven的环境变量设置，或者可以手动将操作路径切换到`{maven-home}/bin`下，再执行maven命令。

**Step 2**：在pom文件的`<dependencies></dependencies>`中添加以下信息

	<dependency>
		<groupId>com.chamc.boot</groupId>
		<artifactId>chamc-boot-starter-bpm</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</dependency>

**Step 3**：在工程目录上右键，选择 `Maven` -> `Update Project` -> `OK`，项目自动编译后，如果没有异常，就能够使用流程服务了。

- 非maven项目的集成：

**Step 1**：在**SVN**获取jar包，下载到本地；
**Step 2**：将jar包添加到项目的build path中，以STS的项目为例：*在项目中添加lib文件夹 -> 把jar包复制到该目录下 -> 鼠标右键jar包 -> Build Path -> add to Bulid Path*， 重新编译项目即可引入流程引擎模块。

#### <span id="bpm_3">3.3.3 第一个流程</span>

- 步骤概述：

> [在管理页面设计流程图（包括流程参与人、操作等的配置）](#bpm_3)  
> [在开发项目中引入`bpm`模块](#maven)  
> [项目中的流程配置](#jdk)  
> [流程发起及操作](#)

- Step 1：<span id="bpm_3">在管理页面设计流程图（包括流程参与人、操作等的配置）</span>

#### <span id="bpm_4">3.3.4 流程设计与管理平台</span>

#### <span id="bpm_5">3.3.5 流程服务</span>

|----|----|----|

#### <span id="bpm_6">3.3.6 SDK接口</span>

#### <span id="bpm_7">3.3.7 错误说明</span>

|----|----|----|
|错误码|错误提示|描述|
|**500**|查询待办发生异常|流程引擎内部错误，可能原因为：1、参数错误，2、流程引擎bug|

#### <span id="bpm_8">3.3.8 同步模块说明</span>

#### <span id="bpm_9">3.3.9 版本变更历史</span>

|----|----|----|
|SDK版本|发布时间|更新内容描述|
|0.0.1SNAPSHOT||新建项目|


## <span id="how-to">4 “How-to”指南</span>

## 6 附录



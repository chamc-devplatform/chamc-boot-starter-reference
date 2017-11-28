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

1. 安装jdk  
根据安装提示进行安装。**注意：安装路径不要包含中文、空格、特殊字符** 
2. 配置环境变量  
  - 新增环境变量JAVA_HOME  

    ![](https://i.imgur.com/aiWkuLN.png)

  - 新增环境变量path(最好将其放在第一位)  

    ![](https://i.imgur.com/r64LoEs.png)

3. 在cmd中执行java -version,若出现下图则安装成功  

  ![](https://i.imgur.com/k5g3ZUa.png)

#### <span id="maven">2.2.2 maven的安装配置</span>

1. 解压apache-maven-3.5.0-bin.zip到某个目录（例如D盘） **注意：解压路径不要包含中文、空格、特殊字符**  
2. 设置环境变量  
  - 新建系统变量  MAVEN_HOME  变量值：D:\apache-maven-3.5.0  

    ![](https://i.imgur.com/Puli2eH.png)  

  - 编辑系统变量  Path  添加变量值：;%MAVEN_HOME%\bin  

    ![](https://i.imgur.com/rJWatOE.png)

3. 打开命令行，输入 mvn --version，若出现以下情况则配置成功。 

  ![](https://i.imgur.com/RifuDqe.png)

4. 在maven目录下的conf文件夹的settings.xml中加入：  
  
  - 在`<proxies>`标签中加入代理：  
  
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

  - 在`<profiles>`标签中加入：  

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
  
  - 在`<activeProfiles>`标签中添加：  

	 	`<activeProfile>jdk-1.8</activeProfile>`
		
#### <span id="sts">2.2.3 spring tool suite的安装配置</span>

1. STS的安装

  - 解压 spring-tool-suite-3.8.4.RELEASE-e4.6.3-win32-x86_64.zip （**注意：解压路径不要包含中文、空格、特殊字符**）
  - 打开 sts-3.8.4.RELEASE 的 STS.exe ，即可使用  
  
2. STS的配置

  - Maven配置  
  
		1. 打开STS，进入windows —》Preferences —》Maven —》Installations。  
		2. 点击Add...选择Maven的所在目录。  
 
		  ![](https://i.imgur.com/ilpB3Wu.png)  

		3. 勾选这个Maven，并点击Apply。    

		  ![](https://i.imgur.com/oMYGNrY.png)  

		4. 选择User Settings，将设置User Settings为自己的settings.xml，OK。  

		  ![](https://i.imgur.com/1quxhE5.png)  

 
  - JDK配置

	  1. 打开STS，进入windows —》Preferences —》Java —》Installed JREs。  

	    ![](https://i.imgur.com/MZgsUFu.png)  

	  2. 选中jdk，选择Edit...。将jdk改为指向jdk的目录（**而不是jre**），Finish。 

	    ![](https://i.imgur.com/W8hkZp6.png)


### 2.3 第一个demo
### 2.4 关于数据库操作的一些介绍

## <span id="component">3 开发平台后端框架组件</span>

### <span id="web">3.1 web组件</span>

#### 3.1.1 配置多数据源及其使用说明
- 1. 简介
- 2. 配置
- 3. demo

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

## <span id="how-to">4 “How-to”指南</span>

## 6 附录



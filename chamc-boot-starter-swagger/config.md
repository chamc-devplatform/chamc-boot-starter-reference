## 配置 ##

1） 添加依赖

在pom.xml中的`<dependencies>`标签中添加依赖

	<dependency>
      <groupId>com.chamc.boot</groupId>
      <artifactId>chamc-boot-starter-swagger</artifactId>
      <version>0.0.1-SNAPSHOT</version>
      <scope>compile</scope>
    </dependency>

2） 修改配置文件

在application.properties文件中增加配置，例如：
    
	chamc.swagger.enable=true
	chamc.swagger.apis.user.group=User
	chamc.swagger.apis.user.path=/user/**,teacher/**

	chamc.swagger.apis.book.group=book
	chamc.swagger.apis.book.path=/book/**


enable=true表示使用swagger组件  
user为自定义表示一个group，可添加多个group的配置，命名不可相同

- user.group表示这个组的名字   
- user.path表示这个组映射的路径，可填多个用 `,` 分隔

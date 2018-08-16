#### 3.2.2 配置

1） 添加依赖

在pom.xml中的`<dependencies>`标签中添加依赖

com.chamc.bootchamc-boot-starter-swagger0.0.1-SNAPSHOT

2） 修改配置文件

在application.properties文件中，增加配置，例如：

```text
chamc.swagger.enable=true
chamc.swagger.apis.user.group=User
chamc.swagger.apis.user.path=/user/**
chamc.swagger.apis.user.title=\u7528\u6237\u63A5\u53E3
```

* enable=true表示使用swagger组件  
* user为自定义表示一个group，可添加多个group的配置，命名不可相同
  * user.group表示这个组的名字
  * user.path表示这个组映射的路径，可填多个用“,”分隔
  * user.title表示这个组api文档的标题  
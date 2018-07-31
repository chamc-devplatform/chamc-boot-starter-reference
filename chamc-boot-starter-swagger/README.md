### 3.2 swagger组件

#### 3.2.1 简介

* 本组件集成了Swagger。Swagger 是一个规范和完整的框架，用于生成、描述、调用和可视化 RESTful 风格的 Web 服务。总体目标是使客户端和文件系统作为服务器以同样的速度来更新。文件的方法，参数和模型紧密集成到服务器端的代码，允许API来始终保持同步。Swagger 让部署管理和使用功能强大的API从未如此简单。了解更多请到[https://swagger.io/](https://swagger.io/)。  
* 关于Swagger UI官方解释是这样的：_The Swagger UI is an open source project to visually render documentation for a Swagger defined API directly from the API’s Swagger specifcation_。Swagger可以将某种固定格式的JSON数据生成可以视图的在线API文档，支持在线测试，可以清楚的观察到IO数据。
* 使用本组件可以获得在线API文档，并能在线测试，节省编写接口文档的时间。

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

#### 3.2.3 demo

1） 示例  
按照以上说明，修改demo-1的配置，启动demo-1，访问`http://localhost:8080/swagger-ui.html`。api文档的标题、介绍已省略。

除此界面以外，还提供另一版本，访问`http://localhost:8080/swagger-ui-3.html`，将检索框url改为`/v2/api-docs?group=User`，group的值为配置文件中设置的group的值。

2） 使用

展开各个接口，输入parameter，点击try it out进行请求。

![](https://i.imgur.com/swqW04D.png) ![](https://i.imgur.com/gOFF6H7.png)

3） 常用标签

* 在swagger-annotations的包中可见有以下标签 ![](https://i.imgur.com/yxjd8Ie.png)
* 最常用的5个注解 @Api：修饰整个类，描述Controller的作用 @ApiOperation：描述一个类的一个方法，或者说一个接口 @ApiParam：单个参数描述 @ApiModel：用对象来接收参数 @ApiProperty：用对象接收参数时，描述对象的一个字段
* 一些简单使用例子

  ```text
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
  ```

详细请参考：[https://github.com/swagger-api/swagger-core/wiki/Annotations\#apimodel](https://github.com/swagger-api/swagger-core/wiki/Annotations#apimodel)、[http://www.cnblogs.com/softidea/p/6251249.html](http://www.cnblogs.com/softidea/p/6251249.html)

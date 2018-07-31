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
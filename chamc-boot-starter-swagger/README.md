# swagger组件

## 简介 ##

swagger组件集成了Swagger。Swagger 是一个规范和完整的框架，用于生成、描述、调用和可视化 RESTful 风格的 Web 服务。总体目标是使客户端和文件系统作为服务器以同样的速度来更新。文件的方法，参数和模型紧密集成到服务器端的代码，允许API来始终保持同步。Swagger 让部署管理和使用功能强大的API从未如此简单。了解更多请到[https://swagger.io/](https://swagger.io/)。  

Swagger UI官方解释：_The Swagger UI is an open source project to visually render documentation for a Swagger defined API directly from the API’s Swagger specifcation_。Swagger可以将某种固定格式的JSON数据生成可以视图的在线API文档，支持在线测试，可以清楚的观察到IO数据。

使用本组件可以获得在线API文档，并能在线测试，节省编写接口文档的时间。

## 配置 ##

1） 添加依赖

在pom.xml中的`<dependencies>`标签中添加依赖

```
<dependency>
	<groupId>com.chamc.boot</groupId>
	<artifactId>chamc-boot-starter-swagger</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<scope>compile</scope>
</dependency>
```

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

## 使用 ##

### 在线API地址 ###
按照以上说明，修改demo-1的配置文件，启动demo-1，访问`http://localhost:8080/swagger-ui.html`。api文档的标题、介绍已省略。

除此界面以外，还提供另一版本，访问`http://localhost:8080/swagger-ui-3.html`，将检索框url改为`/v2/api-docs?group=User`，group的值为配置文件中设置的group的值。

### 使用 ###

展开各个接口，输入parameter，点击try it out进行请求。

![](https://i.imgur.com/swqW04D.png)

![](https://i.imgur.com/gOFF6H7.png)

### 注解说明 ###

常用的几个注解：

- @Api：将一个Controller（Class）标注为一个swagger资源（API）。在默认情况下，Swagger-Core只会扫描解析具有@Api注解的类，而会自动忽略其他类别资源（JAX-RS endpoints，Servlets等等）的注解，重要属性有：
	- tags：API分组标签。具有相同标签的API将会被归并在一组内展示。
	- value：如果tags没有定义，value将作为Api的tags使用
    - description：API的详细描述，在1.5.X版本之后不再使用，但实际发现在2.0.0版本中仍然可以使用

- @ApiOperation：描述一个类的一个方法，或者说一个接口 
- @ApiParam：单个参数描述 
- @ApiModel：用对象来接收参数 描述一个Model的信息（这种一般用在post创建的时候，使用@RequestBody这样的场景，请求参数无法使用@ApiImplicitParam注解进行描述的时候）表明这是一个被swagger框架管理的model，用于class上
- @ApiModelProperty：用对象接收参数时，描述对象的一个字段。表示对model属性的说明或者数据操作更改  
    - value–字段说明 
    - name–重写属性名字 
	- dataType–重写属性类型 
	- required–是否必填 
	- example–举例说明 
	- hidden–隐藏
- @ApiImplicitParams：用在方法上包含一组参数说明
- @ApiResponses：用于表示一组响应
	- @ApiResponse：用在@ApiResponses中，一般用于表达一个错误的响应信息
		- code：数字，例如400
		- message：信息，例如"请求参数没填好"
		- response：抛出异常的类

### 示例 ###

controller配置示例：

	@RestController
	@RequestMapping("/user")
	public class UserController extends BaseRestController<User> {

		private @Autowired UserService service;

		@GetMapping("ageLt")
		@ApiOperation("查询年龄小于age的用户并按倒序排列")//如果不写，默认为接口的函数名
		public ResponseEntity<List<User>> ageLt(@ApiParam(name = "age",value = "年龄",required = true) Long age){
    		List<User> users = service.processAgeLtBusiness(age);
    		return ResponseEntity.ok(users);
		}

		@ApiOperation(value = "/addUser", notes = "添加一组用户")
		@RequestMapping(value = "addUser", method = RequestMethod.POST)
    	@ResponseBody
    	public ResponseEntity<List<User>> addUser(@ApiParam List<User> user){
			//业务逻辑处理
			return ResponseEntity.ok("OK");
    	}

	}

DTO或param配置示例：

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
 
提示：如果使用List传参时出现以下错误，可以在List参数上加注解。如果接收的是 `List<Entity>`，可以使用 `@RequestBody` 或 `@ApiParam` 接收；如果接收 `List<String>` 或其他类型的数组，则可以加上 `@RequestParam` 注解。

	{
	  "timestamp": 1537265963849,
	  "status": 500,
	  "error": "Internal Server Error",
	  "exception": "org.springframework.beans.BeanInstantiationException",
	  "message": "Failed to instantiate [java.util.List]: Specified class is an interface",
	  "path": "/user/addUser"
	}

## 个性化定制 ##

### 背景 ###
swagger是根据spring容器里注册的Docket对象来生成在线文档的，Docket对象有很多关于在线文档生成方面的配置方法，基本配置有设置group，设置path等，我们可以实现DocketCustomizer来设置更多的自定义内容。

### 使用方法 ###

新建Customize类，实现DocketCustomizer，并重写其中customize(Docket docket)方法。

在调用api进行服务请求时，需要在header中加入token才可以通过验证。直接使用swagger调用参数，会出现403错误。这里我们可以实现DocketCustomizer，在全局的请求中加入一个X-SERVICE-ID-TOKEN的参数，这样每次请求时，swagger会帮助我们把填入的token放入header中，验证通过后即可获得正确的返回值。代码示例及效果如下：

	public class TokenDocketCustomize implements DocketCustomizer {
		@Override
		public void customize(Docket docket) {
			ParameterBuilder tokenParamBuilder = new ParameterBuilder();
			Parameter tokenParam = tokenParamBuilder
					.name("X-SERVICE-ID-TOKEN")
					.modelRef(new ModelRef("string"))
					.parameterType("header")
					.required(true)
					.build();
			docket.globalOperationParameters(Arrays.asList(tokenParam));
		}
	}


![](https://i.imgur.com/AAE9wug.png)

## 参考网址 ##
[https://github.com/swagger-api/swagger-core/wiki/Annotations\#apimodel](https://github.com/swagger-api/swagger-core/wiki/Annotations#apimodel)  
[http://www.cnblogs.com/softidea/p/6251249.html](http://www.cnblogs.com/softidea/p/6251249.html)

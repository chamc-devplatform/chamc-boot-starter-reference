# Controller层

## 简介 ##
controller层负责具体的业务模块流程的控制。 
 
controller接收到客户端传来的参数后，首先进行参数校验，校验通过后，调用service层里面的方法进行业务处理，最后将service返回的处理结果进行封装返回给客户端。

书写controller时，要注意restful接口URL的书写规范，也要注意使用三段论的方法处理请求并返回结果。


## controller书写规范 ##
类上加`@Controller`注解，返回结果为页面。  
若加 `@RestController` 注解，则返回 `Return` 里的内容（String/JSON），`@RestController` 注解相当于 `@ResponseBody` ＋ `@Controller`
   
对于Rest接口，Controller请求方法返回类型应为ResponseEntity类型
   
方法体内尽可能三段（行）论：调用校验参数的函数、调用业务服务、产生结果、返回结果

	@RestController
	@RequestMapping("user")
	public class UserController {

		private @Autowired UserService service;

		@GetMapping("/org")
		public ResponseEntity<OrgResult> findOrgList(getOrgListParam param) {
			//校验入参数据并组装业务处理需要的数据
			processFindOrgByUsernameParam(param);
			//调用业务处理方法
			List<Org> org = processFindOrgByUsernameBussiness(param);
			//根据业务处理返回值组装返回给客户端的结果
			OrgResult result = processFindOrgByUsernameResult(org);
			return ResponseEntity.ok(result);
		}

		private void processFindOrgByUsernameParam(getOrgListParam param){
			if(StringUtils.isBlank(param.getUserName())){
				throw new BussinessException("用户姓名不能为空！");
			}
		}
		private List<Org> processFindOrgByUsernameBussiness(getOrgListParam param){
			//调用业务处理方法
			List<Org> org = sercive.findByOrgByUsername(param.getUserName());
			
			//如果业务逻辑比较复杂，则可以在processXXXBussiness中调用多个service方法
			return org;
		}
		private OrgResult processFindOrgByUsernameResult(List<Org> org){
			//将实体对象处理成封装的DTO或result并返回
			//关于封装参数请见后续章节
		}
	}

service的注入可以通过上面的`@Autowired`方式注入，也可以通过构造器注入：
	
	@RestController
	@RequestMapping("user")  
	@RequiredArgsConstructor(onConstructor_ = @Autowired)
	public class UserController  {
		private final @Getter TestService service;
	}
	

建议controller里不要调用controller，调用service
	
当入参的类中存在List里嵌套List，且传输格式不是json时，传入参数解析可能会出现问题，可在配置文件application.properties中设置`chamc.method.complex-argument-support-types`：
	
	chamc.method.complex-argument-support-types=com.chamc.archives.archive.controller.param.PostDocArchiveDetailParam	

## controller参数封装 ##

实体类不能直接返回，需对入参和出参进行封装：

说明：在controller包下新建dto、param、result包，dto存放实体类对应的DTO（可作为入参和出参），param存放入参，result存放出参。  入参类命名为：请求方法+controller方法名+param；出参类命名为：请求方法+controller方法名+result。

	//DTO
	public @Data class OrgDTO {
		private Long id;
		private String orgCode;
		private String orgName;
	}

	//param
	public @Data class GetOrgListParam {
		private Long userId;
		private String userName;
		private String orgName;
	}

	//result
	public @Data class OrgResult {
		private List<OrgDTO> org;
		private long total;
	}

DTO（数据传输对象）与param & result的区别在于：DTO是实体类的对应类，如果入参（或出参）是某一实体的对应类则新建一个DTO，如上面的OrgDTO；  入参可以是param也可以是DTO，出参可以是result或DTO

使用DTO必然会遇到将实体类转换成DTO的情况，可使用开发平台提供的工具类`com.chamc.utils.reflect.BeanUtils`来完成实体之间的属性值拷贝，其中提供的方法有：

- copyProperties(Object source, Object target, CommonCallback callback, String... ignoreProperties)
- copyProperties(Object source, Class<T> targetClass, CommonCallback callBack, String... ignoreProperties)
- copyProperties(List<?> sources, Class<T> targetClass, CommonCallback callBack, String... ignoreProperties)


- copyPropertiesWithClass(Object source, Class<T> targetClass, String... ignoreProperties)
- copyPropertiesWithClass(List<?> sources, Class<T> targetClass, String... ignoreProperties)


- copyNotNullProperties(Object source, Object target, CommonCallback callback, String... ignoreProperties)
- copyNotNullProperties(Object source, Object target, String... ignoreProperties)

既可以拷贝源数据到对象，也可以拷贝源数据到目标类，还可以实现List对象的拷贝。  
可以使用copyNotNullProperties来拷贝不为空的属性值。   
拷贝属性时可以传入回调函数，当相同属性名赋值完成后工具类会调用callBack类进行进一步处理。   
ignoreProperties为被忽略的属性，其中的属性不进行拷贝，可以接收多个值。

注意：使用copyProperties时，只有命名完全相同的属性值才能相互拷贝。
		
## Rest接口url书写规范 ##

单资源\( singular-resourceX \)

    url样例：order/  (order即指那个单独的资源X)   
    GET — 返回一个新的order  
    POST — 创建一个新的order，从post请求携带的内容获取值  

单资源带id\(singular-resourceX/{id} \)

    URL样例：order/1 ( order即指那个单独的资源X )
    GET — 返回id是1的order
    DELETE — 删除id是1的order
    PUT — 更新id是1的order，order的值从请求的内容体中获取。

复数资源\(plural-resourceX/\)

    URL样例:orders/
    GET – 返回所有orders

复数资源查找\(plural-resourceX/search\)

    URL样例：orders/search?name=123
    GET – 返回所有满足查询条件的order资源。(实例查询，无关联) – order名字等于123的。

复数资源查找\(plural-resourceX/searchByXXX\)

    URL样例：orders/searchByItems?name=ipad
    GET – 将返回所有满足自定义查询的orders – 获取所有与items名字是ipad相关联的orders。

单数资源\(singular-resourceX/{id}/pluralY\)
	
    URL样例：order/1/items/ (这里order即为资源X，items是复数资源Y)
    GET – 将返回所有与order id 是1关联的items。

singular-resourceX/{id}/singular-resourceY/

    URL样例：order/1/item/
    GET – 返回一个瞬时的新的与order id是1关联的item实例。
    POST – 创建一个与order id 是1关联的item实例。Item的值从post请求体中获取。

singular-resourceX/{id}/singular-resourceY/{id}/singular-resourceZ/
	
    URL样例：order/1/item/2/package/
    GET – 返回一个瞬时的新的与item2和order1关联的package实例。
    POST – 创建一个新的与item 2和order1关联的package实例，package的值从post请求体中获得。

 上面的规则可以在继续递归下去，并且复数资源后面永远不会再跟随负数资源。  
 总结几个关键点，来更清晰的表述规则。  
 △ 在使用复数资源的时候，返回的是最后一个复数资源使用的实例。  
 △ 在使用单个资源的时候，返回的是最后一个单数资源使用的实例。  
 查询的时候，返回的是最后一个复数实体使用的实例\(们\)。

## 表单验证 ##

对于核心业务功能，除在客户端或浏览器进行数据验证外，还必须在服务器端对数据进行合法性校验，规避用户跳过客户端校验，直接将不合规的保存到应用中。

### 标准验证 ###

spring和web组件实现了一些简单的参数验证的方法，使得接口免去了一些重复的冗余的验证代码。常用的validation有：

Bean Validation 中内置的 constraint: 
      
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
	
Hibernate Validator 附加的 constraint:    

    @NotBlank(message =)   验证字符串非null，且长度必须大于0    
    @Email  被注释的元素必须是电子邮箱地址    
    @Length(min=,max=)  被注释的字符串的大小必须在指定的范围内    
    @NotEmpty   被注释的字符串的必须非空    
    @Range(min=,max=,message=)  被注释的元素必须在合适的范围内 

在入参的类里为需要验证的属性添加validation示例：

	public @Data class GetLoginParam {
		private @NotBlank String username;
		private @Length(min = 6,max = 16)String password;
	}

在controller方法的参数处添加`@Valid`：
		
	@GetMapping("login")
	public ResponseEntity<User> login(@Valid GetLoginParam param){
		return null;
	}

请求时，参数未通过验证就会报400错误：

	{
	    "timestamp": "2017-12-27 15:13",
	    "status": 400,
	    "error": "Bad Request",
	    "exception": "org.springframework.validation.BindException",
	    "errors": [
	        {
	            "codes": [
	                "Length.getLoginParam.password",
	                "Length.password",
	                "Length.java.lang.String",
	                "Length"
	            ],
	            "arguments": [
	                {
	                    "codes": [
	                        "getLoginParam.password",
	                        "password"
	                    ],
	                    "arguments": null,
	                    "defaultMessage": "password",
	                    "code": "password"
	                },
	                16,
	                6
	            ],
	            "defaultMessage": "长度需要在6和16之间",
	            "objectName": "getLoginParam",
	            "field": "password",
	            "rejectedValue": "123",
	            "bindingFailure": false,
	            "code": "Length"
	        }
	    ],
	    "message": "Validation failed for object='getLoginParam'. Error count: 1",
	    "path": "/user/login"
	}


### 扩展验证 ###

有时候用自定义的注解不能完成所有的验证，比如要验证年龄和生日相符，或验证参数必须符合枚举范围，这时我们需要写一个自定义验证注解完成验证。

示例：写一个验证，验证参数必须不为空且都为大写字母。

1） 先写一个注解：

	@Target({ ElementType.FIELD, ElementType.PARAMETER })
	@Retention(RetentionPolicy.RUNTIME)
	@Constraint(validatedBy = UppercaseValidator.class)
	@Documented
	public @interface Uppercase {
	
	    String message() default "必须不为空且全为大写";
	
	    Class<?>[] groups() default { };
	
	    Class<? extends Payload>[] payload() default { };
	
	}

Target指定注解的出现位置：

- ElementType.FIELD 用于某个字段或枚举的常量上，验证字段值符合要求；
- ElementType.PARAMETER 用于验证方法参数符合要求；
- ElementType.TYPE可以用在接口、类、枚举、注解上，验证传入的类符合要求。
	
Retention注解的保留位置:

- RetentionPolicy.SOURCE 注解仅存在于源码中，在class字节码文件中不包含
- RetentionPolicy.CLASS 默认的保留策略，注解会在class字节码文件中存在，但运行时无法获得
- RetentionPolicy.RUNTIME  注解会在class字节码文件中存在，在运行时可以通过反射获取到
		

2） 再写一个类实现ConstraintValidator，返回true或false：

	public class UppercaseValidator implements ConstraintValidator<Uppercase,String>{
	
	    @Override
	    public void initialize(Uppercase constraintAnnotation) {
	        // TODO Auto-generated method stub
	    }
	
	    @Override
	    public boolean isValid(String value, ConstraintValidatorContext context) {
	        if (value != null && value.length() > 0){
	            for(int i = 0; i < value.length(); i++){
	                if (Character.isUpperCase(value.charAt(i))) continue;
	                else return false;
	            }
	            return true;
	        }
	        return false;
	    }
	}


3） 在需要时，同标准验证一样，用自定义注解的地方加上 `@Uppercase` 注解，在调用参数的时候写上 `@Valid` 即可使用。

## 自定义反序列化器 ##

有时候，接口收到的json数据不能直接转化为需要的实体，我们需要自定义反序列化器完成json到实体的转化。web模块为字符串反序列化为实体提供了支持。

### 示例 ###

新建一个类并实现IJsonDeserializer<T>接口，重写其中的deserialize方法，该方法完成字符串到实体的转化。

如下代码实现了将字符串转为大写，再转化为枚举类EnumType的功能。

```
public class EnumTypeDeserializer implements IJsonDeserializer<EnumType> {
	
	private static final long serialVersionUID = 7490487628517157182L;

	@Override
	public EnumType deserialize(String json) {
		EnumType result = EnumType.valueOf(json.toUpperCase());
		return result;
	}

}
```
再将新建的类注入到配置中即可:

```
@Configuration
public class MyConfiguration {

	@Bean
	public IJsonDeserializer<EnumType> enumTypeDeserializer() {
		return new EnumTypeDeserializer();

	}
}
```

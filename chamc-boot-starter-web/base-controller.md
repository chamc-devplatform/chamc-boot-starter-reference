### 2.2 Controller层

1. 简介：controller层负责具体的业务模块流程的控制，controller层主要调用service层里面的方法控制具体的业务流程。

2. controller书写
   * 类上加`@Controller`注解，返回结果为页面。若加 `@RestController` 注解，则返回 `Return` 里的内容（String/JSON），`@RestController` 注解相当于 `@ResponseBody` ＋ `@Controller`
   
   * 对于Rest接口，Controller请求方法返回类型应为ResponseEntity类型
   
   * 方法体内尽可能三段（行）论：调用校验参数的函数、调用业务服务、产生结果、返回结果

			@RestController
			@RequestMapping("user")
    		public class UserController {

				private @Autowired UserService service;

				@GetMapping("/org")
				public ResponseEntity<OrgResult> findOrgList(getOrgListParam param) {
					processFindOrgByUsernameParam(param);//校验入参数据并组装业务处理需要的数据
					List<Org> org = processFindOrgByUsernameBussiness(param);//调用业务处理方法
					OrgResult result = processFindOrgByUsernameResult(org);//根据业务处理返回值组装返回给客户端的结果
					return ResponseEntity.ok(result);
				}

				private void processFindOrgByUsernameParam(getOrgListParam param){
					if(StringUtils.isNotBlank(param.getUserName())){
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

	* service的注入可以通过上面的`@Autowired`方式注入，也可以通过构造器注入：
	
			@RestController
			@RequestMapping("user")  
			@RequiredArgsConstructor(onConstructor_ = @Autowired)
			public class UserController  {
	
				private final @Getter TestService service;
			}
	
	
	* 实体类不能直接返回，需对入参和出参进行封装：

		- 在controller包下新建dto、param、result包，dto存放实体类对应的DTO（可作为入参和出参），param存放入参，result存放出参。  入参类命名为：请求方法+controller方法名+param；出参类命名为：请求方法+controller方法名+result。

				//DTO
        		public @Data class OrgDTO {
           		 	private Long id;
            		private String orgCode;
            		private String orgName;
        		}

				//param
        		public @Data class getOrgListParam {
           			private Long userId;
            		private String userName;
            		private String orgName;
        		}

				//result
        		public @Data class OrgResult {
           		 	private List<OrgDTO> org;
					private long total;
        		}

		- DTO（数据传输对象）与param & result的区别在于：DTO是实体类的对应类，如果入参（或出参）是某一实体的对应类则新建一个DTO，如上面的OrgDTO；  入参可以是param也可以是DTO，出参可以是result或DTO

		- 使用DTO必然会遇到将实体类转换成DTO的情况，可使用工具类 `org.springframework.beans.BeanUtils` 的 `copyProperties(Object source, Object target)` 方法，该类的 `copyProperties(Object source, Object target, String... ignoreProperties)` 可以设置需要忽略的属性
		
				BeanUtils.copyProperties(org, orgDTO,"shortName","sortOrder");//将org的属性值拷贝到orgDTO中，忽略"shortName","sortOrder"字段

		-  注意：使用copyProperties时，只有命名完全相同的属性值才能相互拷贝。
		
	* 建议controller里不要调用controller，调用service
	
	* 当入参的类中存在List里嵌套List，传入参数解析可能会出现问题，可在配置文件application.properties中设置`chamc.method.complex-argument-support-types`：
	
			chamc.method.complex-argument-support-types=com.chamc.archives.archive.controller.param.PostDocArchiveDetailParam

3. Rest接口url书写规范

   * 单资源\( singular-resourceX \)

	        url样例：order/  (order即指那个单独的资源X)   
	        GET — 返回一个新的order  
	        POST — 创建一个新的order，从post请求携带的内容获取值  

   * 单资源带id\(singular-resourceX/{id} \)

	        URL样例：order/1 ( order即指那个单独的资源X )
	        GET — 返回id是1的order
	        DELETE — 删除id是1的order
	        PUT — 更新id是1的order，order的值从请求的内容体中获取。

   * 复数资源\(plural-resourceX/\)

	        URL样例:orders/
	        GET – 返回所有orders

   * 复数资源查找\(plural-resourceX/search\)

	        URL样例：orders/search?name=123
	        GET – 返回所有满足查询条件的order资源。(实例查询，无关联) – order名字等于123的。

   * 复数资源查找\(plural-resourceX/searchByXXX\)

	        URL样例：orders/searchByItems?name=ipad
	        GET – 将返回所有满足自定义查询的orders – 获取所有与items名字是ipad相关联的orders。

   * 单数资源\(singular-resourceX/{id}/pluralY\)
	
	        URL样例：order/1/items/ (这里order即为资源X，items是复数资源Y)
	        GET – 将返回所有与order id 是1关联的items。

   * singular-resourceX/{id}/singular-resourceY/

	        URL样例：order/1/item/
	        GET – 返回一个瞬时的新的与order id是1关联的item实例。
	        POST – 创建一个与order id 是1关联的item实例。Item的值从post请求体中获取。

   * singular-resourceX/{id}/singular-resourceY/{id}/singular-resourceZ/
	
	        URL样例：order/1/item/2/package/
	        GET – 返回一个瞬时的新的与item2和order1关联的package实例。
	        POST – 创建一个新的与item 2和order1关联的package实例，package的值从post请求体中获得。

     上面的规则可以在继续递归下去，并且复数资源后面永远不会再跟随负数资源。  
     总结几个关键点，来更清晰的表述规则。  
     △ 在使用复数资源的时候，返回的是最后一个复数资源使用的实例。  
     △ 在使用单个资源的时候，返回的是最后一个但是资源使用的实例。  
     查询的时候，返回的是最后一个复数实体使用的实例\(们\)。




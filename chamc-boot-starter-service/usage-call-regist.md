# 调用已注册的服务

假设已将应用provider注册，现在应用consumer需要调用provider中的服务。那么，在consumer中进行如下操作即可。

如果只想调用服务，自己并不作为提供者提供服务的话
请在配置中增加
```
eureka.client.register-with-eureka=false
```
即可

## 步骤一

按照上节配置对consumer进行配置。

## 步骤二

新建一个接口，并加注解`@org.springframework.cloud.netflix.feign.FeignClient(name = "xxx")`，其中的参数name必须与需要调用的应用的应用名（即配置文件中的`spring.application.name`）相同，并按照写REST接口的方法书写方法。例如：

假设服务提供方provider有如下接口：

	@GetMapping("hello")
	public ResponseEntity<String> hello() {
	    return ResponseEntity.ok("Hello world!");
	}

则服务消费者consumer应这样调用：

	@FeignClient(name = "provider")
	public interface Client1RemoteService {
	
	    @GetMapping("hello")
	    public String hello();
	
	}

## 步骤三

在需要使用provider服务的地方，注入上一步新建的接口Client1RemoteService即可，如下：

	private @Autowired Client1RemoteService client1;

## 步骤四

在application.properties里新增如下配置：

	#可以配多个，逗号分隔
	chamc.service.feign.scan=com.chamc.xxx,com.chamc.yyy

其中com.chamc.xxx为Client1RemoteService接口所在的包路径。实际应用中，配置所有该类接口所在的包路径。


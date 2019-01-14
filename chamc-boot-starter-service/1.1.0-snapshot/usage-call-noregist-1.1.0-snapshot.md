# 调用未注册的服务

新建一个接口，并加注解`@org.springframework.cloud.netflix.feign.FeignClient(name = "xxx", url = "xxx")`，name任取，url为所要调用的接口的地址。并按照写REST接口的方法书写方法。

例如：需要调用`[GET]http://localhost:8080/hello`接口，则

	@FeignClient(name = "test", url = "http://localhost:8080/")
	public interface Client1RemoteService {
	
	    @GetMapping("hello")
	    public String hello();
	
	}

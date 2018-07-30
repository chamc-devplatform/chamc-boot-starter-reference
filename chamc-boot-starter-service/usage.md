# 使用

**3.4.2.1 如何注册服务**

1） 添加依赖

在pom.xml文件中引入chamc-boot-starter-service的依赖：

com.chamc.bootchamc-boot-starter-service0.0.1-SNAPSHOT

2） 配置文件配置

在application.properties文件中添加配置：

```text
spring.application.name=                 //自定义应用名
chamc.service.registry.token=            //应用对应token
```

【注意】

* token获取：将应用名发给[唐红石](mailto:tanghongshi@chamc.com.cn)获取。
* 注册中心是根据网段（开发、测试、仿真、生产）来判断的，若应用服务器不在相应的网段上，需要手动配置（否则，注册到开发环境上），在配置文件中添加：`eureka.client.service-url.defaultZone=http://XXXXX:8761/eureka,http://XXXXX:8761/eureka`，如需配置地址请联系[唐红石](mailto:tanghongshi@chamc.com.cn)。

这样你的应用就注册到了注册中心，其他应用可以通过注册中心发现你的服务，从而调用你的服务。

**3.4.2.2 如何调用已注册的服务**

假设已将应用provider注册，现在应用consumer需要调用provider中的服务。那么，在consumer中进行如下操作即可。

1. 按照以上配置对consumer进行配置。
2. 新建一个接口，并加注解`@org.springframework.cloud.netflix.feign.FeignClient(name = "xxx")`，其中的参数name必须与需要调用的应用的应用名（即配置文件中的`spring.application.name`）相同，并按照写REST接口的方法书写方法。例如：

   假设服务提供方provider有如下接口：

   ```text
    @GetMapping("hello")
    public ResponseEntity<String> hello() {
        return ResponseEntity.ok("Hello world!");
    }
   ```

   则服务消费者consumer应这样调用：

   ```text
    @FeignClient(name = "provider")
    public interface Client1RemoteService {

        @GetMapping("hello")
        public String hello();

    }
   ```

3. 在需要使用provider服务的地方，注入上一步新建的接口Client1RemoteService即可，如下：

   ```text
    private @Autowired Client1RemoteService client1;
   ```

4. 在application.properties里新增如下配置：

   ```text
    chamc.service.feign.scan=com.chamc.xxx
   ```

   其中com.chamc.xxx为Client1RemoteService接口所在的包路径。实际应用中，配置所有该类接口所在的包路径。
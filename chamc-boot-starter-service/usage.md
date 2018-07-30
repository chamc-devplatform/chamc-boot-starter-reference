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

#### 3.4.3 Feign的使用

整体来说，提供方接口怎么定义，消费方的接口也就怎么定义。

之前的demo只调用了简单的无参接口，下面将介绍几个复杂的出入参接口例子。

1. 带参接口

   假设服务提供方有如下接口：

   ```text
    @PostMapping("person")
    public ResponseEntity<String> create(String name, int age) {
        ……
    }
   ```

   则服务消费者应这样调用：

   ```text
    @PostMapping("/person")
    public String create(@RequestParam("name") String name, @RequestParam("age") int age);
   ```

   【注意】消费者必须在参数上增加`@RequestParam("xxx")`注解，其中xxx必须与提供方的参数名一致。

2. 带`@PathVariable`参数接口

   假设服务提供方有如下接口：

   ```text
    @GetMapping("/person/{id}")
    public ResponseEntity<String> get(@PathVariable("id") int id) {
        ……
    }
   ```

   则服务消费者应这样调用：

   ```text
    @GetMapping("/person/{id}")
    public String getPerson(@PathVariable("id") int id);
   ```

3. 返回Json接口

   假设服务提供方有如下接口：

   ```text
    @GetMapping("/person/{id}")
    public ResponseEntity<Person> get(@PathVariable("id") int id) {
        ……
    }
   ```

   Person：

   ```text
    public @Data class Person {
        private Long id;
        private String name;
        private int age;

        @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss.S Z", timezone = "GMT+8")
        private Date birthday;
        private Address address;
    }
   ```

   Address：

   ```text
    public @Data class Address {
        private String street;
        private int no;
    }
   ```

   则服务消费者应这样调用：

   ```text
    @GetMapping("/person/{id}")
    public User user(@PathVariable("id") Long id);
   ```

   User：

   ```text
    public @Data class User {
        private String name;
        private int age;

        @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss.S Z", timezone = "GMT+8")
        private Date birthday;
        private Address address;
    }

    public @Data class Address {
        private String street;
        private int no;
        private String city;
    }
   ```

   【注意】消费方可以只接收自己需要的数据，与提供方属性名一致即可。

4. 接收Json参数接口

   假设服务提供方有如下接口：

   ```text
    @PostMapping("")
    public ResponseEntity<Person> create(@RequestBody Person person){
        return ResponseEntity.ok(person);
    }
   ```

   则服务消费者应这样调用：

   ```text
    @PostMapping("")
    public User create(@RequestBody User user);
   ```

#### 3.4.4 其他配置介绍

1. `chamc.service.feign.log-level`：在消费方设置，用于指定消费方调用提供方服务时打印日志的级别，包括none、headers、basic、full四个级别。默认为none。

   full级别日志打印示例：

   ```text
    [Client1RemoteService2#create] ---> POST http://provider/create HTTP/1.1       
    [Client1RemoteService2#create] Content-Type: application/json;charset=UTF-8
    [Client1RemoteService2#create] Content-Length: 86
    [Client1RemoteService2#create]
    [Client1RemoteService2#create] {"name":"abc","age":12,"birthday":null,"address":{"street":null,"no":123,"city":"BJ"}}
    [Client1RemoteService2#create] ---> END HTTP (86-byte body)
    ......// 以上是发送请求时的日志，以下是接收到返回结果的日志
    [Client1RemoteService2#create] <--- HTTP/1.1 200 (660ms)
    [Client1RemoteService2#create] content-type: application/json;charset=UTF-8
    [Client1RemoteService2#create] date: Fri, 02 Mar 2018 07:05:40 GMT
    [Client1RemoteService2#create] transfer-encoding: chunked
    [Client1RemoteService2#create] x-application-context: provider:8899
    [Client1RemoteService2#create] 
    [Client1RemoteService2#create] {"id":null,"name":"abc","age":12,"birthday":null,"address":{"street":null,"no":123}}
    [Client1RemoteService2#create] <--- END HTTP (84-byte body)
   ```

## 4 “How-to”指南

## 5 附录


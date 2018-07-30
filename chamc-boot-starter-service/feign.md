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
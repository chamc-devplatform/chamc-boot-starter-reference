#### 3.1.1 配置多数据源及其使用说明

1） 简介

该组件支持配置多个数据源，即可对多个数据库进行操作，详细使用方法见下。

2） 配置

* 在配置文件application.properties中，开启多数据源并配置默认数据源（**注意：如果之前配了数据库，需删除之前配置**），def指默认数据源，当没有指定数据源时，默认使用该数据源。默认数据源必配！

  ```text
    chamc.ds.compose.enable=true
    chamc.ds.compose.def.url=jdbc:mysql://localhost:3306/test?characterEncoding=utf8&useSSL=true
    chamc.ds.compose.def.username=root
    chamc.ds.compose.def.password=1111
  ```

* 配置其他数据源，例如命名为：test，则配置如下

  ```text
    chamc.ds.compose.data-sources.test.url=jdbc:mysql://localhost:3306/test2?characterEncoding=utf8&useSSL=true
    chamc.ds.compose.data-sources.test.username=root
    chamc.ds.compose.data-sources.test.password=1111
  ```

  test由自己定义，可再使用不同的命名继续增加数据源

* 除了以上配置，还有一个配置chamc.ds.compose.pointcut，表示对于@TargetDataSource注解的解析应用在哪些地方，默认是继承BaseService的类中@TargetDataSource生效。若不想继承BaseService也想使其生效，则需通过aspectJ配置方式进行配置。

3） demo

* 使用代码生成工具生成repository和entity，参见[2.3.1 — 4 — 3）](chamc-boot-starter-web.md#codegenerate)
* 当需要使用test数据源时，在其service里的方法上加标签**@TargetDataSource**
* 例如：写一个接口，当type=0时返回用户信息，否则返回书的信息

在controller中：

```text
    @GetMapping("test")
    public ResponseEntity<?> bookOrUsers(@RequestParam Long type){
        if (type.equals(0L)){
            List<User> users = service.findUsers();
            return ResponseEntity.ok(users);
        }else {
            List<Book> books = service.findBooks();
            return ResponseEntity.ok(books);
        }
    }
```

在service中：

```text
    public List<User> findUsers() {
        return repository.findAll();
    }

    @TargetDataSource("test")
    public List<Book> findBooks() {
        return bookRepository.findAll();
    }
```

运行结果如下：

请求：`http://localhost:8080/user/test?type=0`

```text
    [
      {
        "id": 1,
        "username": "test001",
        "password": "111111",
        "userdetailId": 1,
        "new": false
      },
      {
        "id": 2,
        "username": "test002",
        "password": "222222",
        "userdetailId": 2,
        "new": false
      }
    ]
```

请求：`http://localhost:8080/user/test?type=1`

```text
    [
      {
        "id": 1,
        "name": "三国演义",
        "price": 56,
        "writer": "罗贯中",
        "new": false
      },
      {
        "id": 2,
        "name": "测试书本",
        "price": 12,
        "writer": "测试",
        "new": false
      }
    ]
```

**注意**：  
1. 当一个controller中需要使用多个数据源的数据，应该在controller中调用多个service方法，而不是在service中调用service  
2. 使用哪一个数据源进行操作，取决于第一次进入service中指定的数据源  
3. 不指定数据源时，均使用默认数据源  
4. 错误示例：controller调用service中的processBookOrUsers2Bussiness\(\)方法后，数据源已经指定为默认数据源，在 processBookOrUsers2Bussiness\(\)中再调用service的findBooks\(\)方法，findBooks\(\)上配置的test数据源将不起作用。数据源已第一次进入service时指定的数据源为准。

controller中：

```text
    @GetMapping("test")
    public ResponseEntity<?> bookOrUsers2(@RequestParam Long type){
        ResponseEntity<?> result = service.processBookOrUsers2Bussiness(type);
        return result;
    }
```

service中：

```text
    public ResponseEntity<?> processBookOrUsers2Bussiness(Long type) {
        if (type.equals(0L)){
            List<User> users = this.findUsers();
            return ResponseEntity.ok(users);
        }else{
            List<Book> books = this.findBooks();
            return ResponseEntity.ok(books);
        }
    }

    public List<User> findUsers() {
        return repository.findAll();
    }

    @TargetDataSource("test")
    public List<Book> findBooks() {
        return bookRepository.findAll();
    }
```
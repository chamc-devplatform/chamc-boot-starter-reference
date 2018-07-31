### 3.1 web组件

#### 3.1.0 基础信息配置

```text
chamc.web.scan.repository=com.chamc.archive     #业务系统的repository的扫描配置，业务系统的repository不需要再加@Repository注解
chamc.web.scan.entity=com.chamc.archive     #业务系统的entity扫描配置
```

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

#### 3.1.2 安全相关功能及其使用说明

1） 简介

该组件提供用户组织机构数据同步、域登陆、权限控制等安全相关功能，详细使用方法如下。

2） 同步用户组织机构数据功能使用说明

**【准备工作】**

* 第一步：建表，新建以下7张表：t_sys\_org，t\_sys\_permission，t\_sys\_role，t\_sys\_role\_permission，t\_sys\_user，t\_sys\_user\_org，t\_sys\_user\_role。在jar包中获取建表脚本:\`init\_sys_\[mysql\|oracle\].sql\`。
* 第二步：同步用户、组织数据。

**【同步用户-组织机构数据】**

**【1】**同步程序设计说明

* 同步程序需要多数据源的支持，数据源来自人力系统`EntUserDb`,目标数据库是业务系统数据库，entuserdb和sys的数据库er图如下所示；
* 同步模块可配置使用或关闭，如下“使用demo”中的配置项所示；
* 同步程序以两种方式提供服务，定时任务+service调用；
* 同步程序使用多线程处理，相对来说效率较高，线程池可配置，如下“使用demo”中的配置项所示，多线程处理后会等待处理结果，然后再继续执行下一步逻辑；
* 同步程序可以添加干预listener，只需要继承`SyncOperationListenerAdapter`或者实现`SyncOperationListener`即可；
* 同步按照`机构 -> 用户 -> 用户机构`或者`用户 -> 机构 -> 用户机构`的顺序执行，

![entuserdb ER&#x56FE;](https://i.imgur.com/IS2yDal.jpg)

图：EntUserDb ER图

![&#x6743;&#x9650;&#x6A21;&#x5757;&#x6570;&#x636E;&#x5E93;&#x6A21;&#x578B;&#x56FE;](https://i.imgur.com/iZKWdRN.jpg)

图：sys\_ ER图

**【2】**使用

前置条件：1、已根据第一步的数据库初始化脚本在数据库中间表；

步骤1：在项目的`application.propertis`中添加配置项，如下所示：

```text
chamc.web.sync.enable=true   #是否启用同步模块
chamc.web.sync.operatorId=1  #同步的操作人id
#chamc.web.sync.timer.enable=true     #（可选）是否启用定时同步任务，默认为false
#chamc.web.sync.timer.cron=0 27 11 17 1 ?    #（可选）定时任务的任务计划，为cron表达式，默认值为每天晚上九点同步（0 0 21 * * *），corn说明参考[3、cron简单示例]

#chamc.web.threadpool.core-pool-size=50     #（可选）线程池配置，实例中数字为默认值
#chamc.web.threadpool.max-pool-size=200     #（可选）
#chamc.web.threadpool.queue-capacity=500        #（可选）
```

步骤2：运行同步程序

如果添加配置项的时候，配置了启用定时同步任务（`chamc.web.sync.timer.enable=true`），则会自动按照定时任务计划（`cron`）运行同步程序，不需要再添加其他代码调用。

ps：enteruserdb数据源为sqlserver数据库，请注意为业务系统添加sqlserver依赖，如下所示：

com.microsoft.sqlserversqljdbc44.0runtime

如果需要手动调用同步程序，只需要在业务系统代码中注入对应的`syncService`并调用同步方法即可，如下所示：

```text
@RestController
public class SyncController {

    //import com.chamc.boot.web.support.sys.service.SyncService;
    private @Autowired SyncService service;

    /**
     * 同步机构（包含enteruserdb中对应的role）
     */
    @GetMapping("org")
    public void syncOrg() {
        service.syncOrg();
    }

    /**
     * 同步用户
     */
    @GetMapping("user")
    public void syncUser() {
        service.syncUser();
    }

    /**
     * 同步"用户-机构关系"
     */
    @GetMapping("userorg")
    public void syncUserOrg() {
        service.syncUserOrg();
    }

    /**
     * 同步"用户，机构（enteruserdb的角色），用户-机构关系"
     */
    @GetMapping("sync")
    public void sync() {
        service.sync();
    }

    @GetMapping("syncTest")
    public void syncTest() {
        service.syncThread();
    }
}
```

如果需要对同步程序进行干预，可采用以下方式添加bean并注入到系统中，可以有多个listener共存，共同起作用：

* 方法1：继承`SyncOperationListenerAdapter`实现需要的方法
* 方法2：继承`SyncOperationListener`并实现其中的方法

示例：

```text
/**
 * beforeOrg方法：方法体中可以修改org，该org为直接写入到数据库的org，如果需要过滤掉该org返回false，否则需要返回true
 * beforeUser方法：同beforeOrg，order为监听顺序
**/
//listener
public class SyncListener extends SyncOperationListenerAdapter {

    //顺序默认为999，从小到大排列
    @Override
    public int order() {
        return super.order();
    }

    @Override
    public boolean beforeOrg(Org org) {
        return super.beforeOrg(org);
    }

    @Override
    public boolean beforeUser(User user) {
        return super.beforeUser(user);
    }
}

//listener1
public class SyncListener1 implements SyncOperationListener {


    @Override
    public int order() {
        return 0;
    }

    @Override
    public boolean beforeUser(User user) {
        if(user.getAccount().equals("xiaojia@chamc.com.cn@4869")) {
            log.info("syncListener1");
            return true;
        }
        return false;
    }

    @Override
    public boolean beforeOrg(Org org) {
        if(org.getCode().equals("ORG100000012")) {
            return true;
        }
        return false;
    }

    @Override
    public void afterThrowing(Object obj, Throwable t) {
    }
}


//注入 listener（**仅供参考**）
@Configuration
public class SyncAutoConfig {

    public @Bean SyncListener syncListener() {
        return new SyncListener();
    }

    public @Bean SyncListener1 syncListener1() {
        return new SyncListener1();
    }
}
```

**【3】**cron简单示例

ps：可网上在线搜索“cron在线表达式”，在线获得表达式，粘贴使用即可，另[cron说明直通车](http://blog.csdn.net/u011116672/article/details/52517247)

cron表达式为6位的、用空格分隔的字符串

```text
0 0 21 * * *
```

如上示例，6位的表达式，分别为`second minute hour day month weekday`；

\|----\|----\|----\|----\|----\| \|类别\|是否必须\|值范围\|可用符号\|附注\| \|second\|是\|0-59\|`*` `,` `-` `\`\|`\`表示每隔多久执行\| \|minute\|是\|0-59\|`*` `,` `-` `\`\|`*`表示通配任意匹配，`,`表示可以有多个值，用`,`隔开，`-`表示一个区间范围\| \|hour\|是\|0-23\|`*` `,` `-` `\`\|\| \|day\|是\|1-31\|`*` `,` `-` `\` `?`\|`?`表示当同时配置了星期时，与星期互斥，即是这一天但不是星期n的时候，在这一天执行\| \|month\|是\|1-12/JAN-DEC\|`*` `,` `-` `\`\|\| \|weekday\|是\|1-7/SUN-SAT\|`*` `,` `-` `\` `?`\|1表示星期一\|

示例：

0 0 21  __ _：表示每天晚上九点整执行任务  
\30 0 21_   __：表示每天晚上九点的第一分钟，每隔30秒执行一次任务  
0 0-30 21,22  __ \*:表示每天晚上九点到九点半、十点到十点半都执行任务

3） 域登陆配置

* 在application.properties文件中开启security，并配置不需要验证的url和不需要做验证登录的url，例如：

  ```text
    chamc.security.enable=true
    chamc.security.permission.enable=true
    chamc.security.addtional-none-login-urls=/loginUrl
  ```

* 登录请求分为两种类型：ajax（rest请求，前后端分离）和normal（前后端不分离），配置如下：
  * ajax类型，url是ad登陆的服务url，appName为与系统标识名称，retUrl为回调地址。

    ```text
        #ajax
        chamc.security.ad.login-type=ajax
        chamc.security.ad.url=http://10.1.8.81/ChamcSSO/LoginWin.ashx
        chamc.security.ad.app-name=TestApp
        chamc.security.ad.ret-url=http://localhost:8080/ajaxLogin
    ```

  * normal类型，需配置验证成功的跳转地址。

    ```text
        #normal
        chamc.security.ad.login-type=normal
        chamc.security.ad.success-url=/index
    ```
* 用户信息会放入redis缓存里，所以也要设置redis地址：

  ```text
        spring.redis.host=10.1.8.119
  ```

4） 域登陆demo

* 新建以下7张表：t_sys\_org，t\_sys\_permission，t\_sys\_role，t\_sys\_role\_permission，t\_sys\_user，t\_sys\_user\_org，t\_sys\_user\_role。在jar包中获取建表脚本:\`init\_sys_\[mysql\|oracle\].sql\`。
* 在库中插入数据，包括用户、角色、权限等。以下是可用的几个域用户，角色、机构、权限请自行插入：

  ```text
    INSERT INTO `t_sys_user` (`id_`,`account_`,`name_`,`password_`,`email_`,`sort_order_`,`domain_account_`,`account_name_with_domain_`,`is_domain_account_`,`mobile_`,`home_phone_`,`room_no_`,`office_phone_`,`office_short_phone_`,`status_`,`gmt_create_`,`create_user_`,`gmt_update_`,`update_user_`,`gmt_status_up_`,`status_up_user_`,`gmt_status_down_`,`status_down_user_`) VALUES (1,'user1@kmdev.com.cn','用户1',NULL,'user1@dev.com',NULL,'dev\\user1',NULL,'1',NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL);
    INSERT INTO `t_sys_user` (`id_`,`account_`,`name_`,`password_`,`email_`,`sort_order_`,`domain_account_`,`account_name_with_domain_`,`is_domain_account_`,`mobile_`,`home_phone_`,`room_no_`,`office_phone_`,`office_short_phone_`,`status_`,`gmt_create_`,`create_user_`,`gmt_update_`,`update_user_`,`gmt_status_up_`,`status_up_user_`,`gmt_status_down_`,`status_down_user_`) VALUES (2,'leader1@kmdev.com.cn','领导1',NULL,'leader1@dev.com',NULL,'dev\\leader1',NULL,'1',NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL);
    INSERT INTO `t_sys_user` (`id_`,`account_`,`name_`,`password_`,`email_`,`sort_order_`,`domain_account_`,`account_name_with_domain_`,`is_domain_account_`,`mobile_`,`home_phone_`,`room_no_`,`office_phone_`,`office_short_phone_`,`status_`,`gmt_create_`,`create_user_`,`gmt_update_`,`update_user_`,`gmt_status_up_`,`status_up_user_`,`gmt_status_down_`,`status_down_user_`) VALUES (3,'user2@kmdev.com.cn','用户2',NULL,'user2@dev.com',NULL,'dev\\user2',NULL,'1',NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL,NULL);
  ```

* 写一个接口获取ad登录地址

  ```text
    @GetMapping("loginUrl")
    public String loginUrl(){
        String loginUrl = securityProperties.getAd().getUrl() + "?appName=" + securityProperties.getAd().getAppName() 
        + "&retUrl=" + securityProperties.getAd().getRetUrl();
        return loginUrl;    
    }
  ```

* 请求该接口，获取登录地址进行登录。

5） 权限控制

开启security之后，可使用注解进行权限控制，目前仅支持前后端不分离的模式，前端模板使用thymeleaf。

有三种安全注解可供使用：`@Secured`注解、`@RolesAllowed`注解以及表达式驱动的注解（`@PreAuthorize`、`@PostAuthorize`、`@PreFilter`和`@PostFilter`）。推荐使用表达式驱动的注解。

* **建议**启用用户组织机构数据同步服务，此时应已执行建表脚本:`init_sys_[mysql|oracle].sql`。

  插入角色、权限等数据。注意：权限表和角色表中的`status_`为1时，表示该权限或角色启用。

**【后端权限控制】**

在方法上加注解来控制方法的权限。

**\[1\]** `@Secured`和`@RolesAllowed`类似，使用一个String数组作为参数，每个String值是一个权限，调用这个方法至少需要具备其中的一个权限。例如以下两个例子，用户要访问add方法必须有`ADD`角色或者`ADMIN`角色之一；用户要访问delete方法必须有`DELETE`角色或者`ADMIN`角色之一。

```text

@Secured({"ROLE_ADD","ROLE_ADMIN"})
public String add(){
   ……
   return "add";
}

@RolesAllowed({"ROLE_DELETE","ROLE_ADMIN"})
public String delete(){
   ……
   return "delete";
}
```

这两个注解再拒绝未认证用户方面还不错，但是都存在一个缺点：它们只能根据用户有没有授予特定的权限来限制方法的调用，在判断方式是否执行方面，无法使用其他的因素。为此，Spring Security 3.0引入了表达式驱动的注解。

**\[2\]** 表达式驱动的注解：这些注解的值参数中都可以接受一个SpEL表达式（[了解更多](https://docs.spring.io/spring-security/site/docs/5.0.0.RELEASE/reference/htmlsingle/#el-access)）。表达式可以是任意合法的SpEL表达式，如果表达式的计算结果为true，那么安全规则通过，否则就会失败。安全规则通过或失败的结果会因为所使用注解的差异而有所不同。

**【常用的几个EL表达式】**

```text

hasAnyRole([role1,role2])  //如果用户被授予了列表中任意的指定角色，结果为true
hasRole([role])            //如果用户被授予了指定的角色，结果为true
hasAuthority([authority])  //如果用户有指定的权限，结果为true
hasAnyAuthority([authority1,authority2])  //如果用户有列表中任意的权限，结果为true
```

示例：

```text

@PreAuthorize("hasRole('ADMIN')")
public String list(){……}

@PreAuthorize("hasAnyAuthority('project:view','project:*')")
public String list(){……}
```

**【四个注解】**

```text

@PreAuthorize   //在方法调用之前，基于表达式的计算结果来限制对方法的访问
@PostAuthorize  //允许方法调用，但是如果表达式计算结果为false，将抛出一个安全性异常
@PostFilter     //允许方法调用，但必须按照表达式来过滤方法的结果
@PreFilter      //允许方法调用，但必须在进入方法之前过滤输入值
```

**@PreAuthorize** 表达式会在方法调用之前执行，如果表达式的计算结果不为true的话，将会阻止方法执行。与`@Secured`和`@RolesAllowed`相比，除了可以限制角色以外，还可以进行参数的验证。例如：

```text

@PreAuthorize("hasAuthority('project:view') and #size>0")
@GetMapping("view")
public String list(int size) {
   return "project";
}
```

 用户有\`project:view\`权限，并且入参size&gt;0时，才能访问该方法。 \*\*@PostAuthorize\*\* 表达式直到方法返回才会执行，然后决定是否抛出安全性的异常。除了验证的时机之外，\`@PostAuthorize\`与\`@PreAuthorize\`的工作方式差不多，只不过它会在方法执行之后，才会应用安全规则。例如： @PostAuthorize\("returnObject.size\(\) == \#size"\) @GetMapping\("postAuth"\) public List testPost\(int size\) { …… return books; } \`returnObject\`表示返回的对象，本例中若返回列表中对象的个数与size不等，将抛出异常。 \*\*@PostFilter\*\* 会使用这个表达式计算该方法所返回集合的每个成员，将计算结果为false的成员移除掉。例如： \*\*@PreFilter\*\* 过滤的是要进入方法中的集合成员，只有满足SpEL表达式的元素才会留在集合中。 \*\*【前端的权限控制】\*\* 在标签中加\`sec:authorize\`属性，其值也是SpEL表达式。例如：\`

查看项目

\`，当用户有\`project:view\`的权限，这个\`

\`才会显示在页面中。 \*\*【相关类介绍】\*\* - com.chamc.boot.web.utils.SecurityUtils：提供获取当前用户信息（包括用户信息，角色及权限信息，主机构，兼职机构）和切换机构的方法。 static com.chamc.boot.web.support.sys.service.LoginUser getCurrentUser\(\); static void switchOrg\(Long orgId\); //orgId为目标机构Id - LoginUser：当前用户信息 com.chamc.boot.web.support.sys.model.User getUser\(\); //用户信息 List getAuthorites\(\); //权限信息：包括角色和权限 List getConcurrentOrgs\(\); //用户兼职机构 List getOrgs\(\); //用户的所有机构 Optional getMainOrg\(\); //用户的主机构（可为空） Optional getCurrentOrg\(\); //获取当前机构 Optional&gt; getCurrentRoles\(\); //获取当前角色 Optional&gt; getRoles\(\); //获取所有角色 \#\#\#\# 3.1.3 配置日志打印及其使用说明 1） 简介 该组件提供对各层（controller、service、repository）进行日志跟踪，详细使用方法如下。 2） 配置 - 在application.properties文件中开启日志跟踪： chamc.tracelog.enable=true 请求某个接口后，如下： 2017-12-27 16:12:07.608 TRACE 988 --- \[nio-8080-exec-9\] c.c.b.w.config.log.TraceLogInterceptor : Entering UserController.bookOrUsers\(Long\) 2017-12-27 16:12:07.612 TRACE 988 --- \[nio-8080-exec-9\] c.c.b.w.config.log.TraceLogInterceptor : Entering UserService.findBooks\(\) 2017-12-27 16:12:07.620 TRACE 988 --- \[nio-8080-exec-9\] c.c.b.w.config.log.TraceLogInterceptor : Entering .Proxy192.findAll\(\) Hibernate: select book0\_.id as id1\_4\_, book0\_.name as name2\_4\_, book0\_.price as price3\_4\_, book0\_.writer as writer4\_4\_ from t\_book book0\_ 2017-12-27 16:12:07.714 TRACE 988 --- \[nio-8080-exec-9\] c.c.b.w.config.log.TraceLogInterceptor : Leaving .Proxy192.findAll, took 94ms 2017-12-27 16:12:07.714 TRACE 988 --- \[nio-8080-exec-9\] c.c.b.w.config.log.TraceLogInterceptor : Leaving UserService.findBooks, took 102ms 2017-12-27 16:12:07.714 TRACE 988 --- \[nio-8080-exec-9\] c.c.b.w.config.log.TraceLogInterceptor : Leaving UserController.bookOrUsers, took 106ms - 同时可以对各层进行配置： !\[\]\(https://i.imgur.com/8e6mqVi.png\) 其中XXXX代表controller、service、repository： chamc.tracelog.XXXX.enable /\*\*是否开启，默认false\*/ chamc.tracelog.XXXX.showEnter /\*\* 是否打印Enter日志 ，默认true \*/ chamc.tracelog.XXXX.showExit /\*\* 是否打印Exit日志 \*/ chamc.tracelog.XXXX.className /\*\* 日志内容是否包含类名，默认true \*/ chamc.tracelog.XXXX.methodName /\*\* 日志内容是否包含方法名，默认true \*/ chamc.tracelog.XXXX.arguments /\*\* Enter日志内容是否包含参数值，谨慎开启，可能会影响性能 ，默认false \*/ chamc.tracelog.XXXX.argumentTypes /\*\* Enter日志内容是否包含参数类型，默认true\*/ chamc.tracelog.XXXX.returnValue /\*\* Exit日志内容是否包含返回值，谨慎开启，肯会影响性能 ，默认false \*/ chamc.tracelog.XXXX.invocationTime /\*\* Exit日志是否包含执行时间，单位：ms ，默认true \*/ chamc.tracelog.XXXX.pointcut /\*\* 日志切入点 \*/ 3） demo 在application.properties配置如图所示： chamc.tracelog.enable=true chamc.tracelog.controller.enable=true chamc.tracelog.service.enable=true chamc.tracelog.service.return-value=true chamc.tracelog.repository.enable=true chamc.tracelog.repository.arguments=true 请求一个接口，service的返回值和repository的入参被跟踪打印，如下： 2017-12-27 16:22:33.064 TRACE 988 --- \[io-8080-exec-10\] c.c.b.w.config.log.TraceLogInterceptor : Entering UserController.bookOrUsers\(Long\) 2017-12-27 16:22:33.077 TRACE 988 --- \[io-8080-exec-10\] c.c.b.w.config.log.TraceLogInterceptor : Entering UserService.findBooks\(\) 2017-12-27 16:22:33.086 TRACE 988 --- \[io-8080-exec-10\] c.c.b.w.config.log.TraceLogInterceptor : Entering .Proxy202.findAll\(\)\(\) 2017-12-27 16:22:33.129 INFO 988 --- \[io-8080-exec-10\] o.h.h.i.QueryTranslatorFactoryInitiator : HHH000397: Using ASTQueryTranslatorFactory Hibernate: select book0\_.id as id1\_4\_, book0\_.name as name2\_4\_, book0\_.price as price3\_4\_, book0\_.writer as writer4\_4\_ from t\_book book0\_ 2017-12-27 16:22:33.135 TRACE 988 --- \[io-8080-exec-10\] c.c.b.w.config.log.TraceLogInterceptor : Leaving .Proxy202.findAll\(\), took 49ms 2017-12-27 16:22:33.135 TRACE 988 --- \[io-8080-exec-10\] c.c.b.w.config.log.TraceLogInterceptor : Leaving UserService.findBooks with return value \[Book\(id=1, name=三国演义, price=56, writer=罗贯中\), Book\(id=2, name=测试书本, price=12, writer=测试\)\], took 58ms 2017-12-27 16:22:33.135 TRACE 988 --- \[io-8080-exec-10\] c.c.b.w.config.log.TraceLogInterceptor : Leaving UserController.bookOrUsers, took 71ms \#\#\#\# 3.1.4 定时任务模块 【定时任务模块设计说明】 - 定时任务模块使用开源作业调度框架Quartz实现，简化业务系统配置和使用 - 业务系统可以方便地添加任务，并使用Quartz管理用户组织机构同步任务 - 默认使用内存单应用模式，无需业务系统在数据库建表，可配置为使用数据库和集群模式 - 提供Quartz默认配置，业务系统可在配置文件中覆盖 - 提供对Scheduler注入监听器的功能 【传送门】 \[Quartz-2.2.x-官方教程\]\(http://www.quartz-scheduler.org/documentation/quartz-2.2.x/tutorials/ "Quartz-2.2.x-官方教程"\) \[Quartz基本使用\]\(https://blog.csdn.net/gjb724332682/article/details/53019953 "Quartz基本使用"\) 【配置】 （1）Quartz相关配置说明 - 集群模式下必须且默认使用数据库存储 - \*\*集群所有节点应使用时间同步服务，时间误差应在1s内\*\*

```

chamc.quartz.mode=monomer                #Quartz模式  单应用(MONOMER)、集群(CLUSTER)
chamc.quartz.store-type=jdbc            #存储模式   内存(MEMORY)、数据库(JDBC)

#（可选）Quartz数据库名称，使用Spring数据源的时候不用配置，在使用多数据源时默认使用缺省数据源，可以指定为任意数据源名称
chamc.quartz.data-source-name=test     

#（可选）覆盖默认配置样例，其他可覆盖配置详见下面配置文件
#覆盖方式为“chamc.quartz.properties” 加 Quartz配置
chamc.quartz.properties.org.quartz.scheduler.instanceName=ChamcQuartzScheduler

```

（2）可支持覆盖的Quartz配置及默认值

```text

# 可为任何值,用在jdbc jobstrore中来唯一标识实例，但是在所有集群中必须相同
org.quartz.scheduler.instanceName=ChamcScheduler

# Configure ThreadPool  执行任务线程池配置
#============================================================================
#线程池类型，执行任务的线程
org.quartz.threadPool.class=org.quartz.simpl.SimpleThreadPool
#线程数量
org.quartz.threadPool.threadCount=10
#线程优先级
org.quartz.threadPool.threadPriority=5
org.quartz.threadPool.threadsInheritContextClassLoaderOfInitializingThread=true 

#任务被判定为错失的超时时间
org.quartz.jobStore.misfireThreshold: 60000

#数据库表前缀
org.quartz.jobStore.tablePrefix=T_QRTZ_

#属性定义了Scheduler实例检入到数据库中的频率(单位：毫秒)。默认值是 15000 (即15 秒)。
org.quartz.jobStore.clusterCheckinInterval=15000

#是否打开RMI支持
org.quartz.scheduler.rmi.export=false
#是否打开RMI代理
org.quartz.scheduler.rmi.proxy=false

```

（3）使用

【简易使用——直接注入任务和对应触发器】

* 定时任务模块对注入的Trigger和对应的Job进行提交，因此Trigger注入时**必须使用.forJob（）方法**
* 任务类应实现Job或继承QuartzJobBean

  // 注入任务Bean，storeDurably\(\)使任务在没有对应触发器时不会被删除

  // Job建议设置名称和组 .withIdentity\(\[name\], \[group\]\)

  public @Bean JobDetail job\(\) {

    return JobBuilder.newJob\(SampleJob.class\).storeDurably\(\).build\(\);

  }

// 注入触发器Bean，使用forJob与任务关联 // Trigger建议设置名称和组 .withIdentity\(\[name\], \[group\]\) public @Bean Trigger trigger\(\) { return TriggerBuilder.newTrigger\(\).forJob\(job\(\)\).startNow\(\) .withSchedul\(DailyTimeIntervalScheduleBuilder.dailyTimeIntervalSchedule\(\).withIntervalInSeconds\(5\)\) .withIdentity\(\[name\], \[group\]\).build\(\); }

//任务类  
@Slf4j public static class SampleJob extends QuartzJobBean {

```text
@Override
protected void executeInternal(JobExecutionContext arg0) throws JobExecutionException {
    log.info("样例任务");
}
```

} &lt;/pre&gt;

【基本使用——使用JobDataMap传参、更多触发器种类】

* 触发器构建与启动计划任务

  //注入Scheduler

  private @Autowired Scheduler scheduler;

JobDataMap data = new JobDataMap\(\); //示例内容，对应下面Job类中的属性 data.put\("from", from\); data.put\("target", target\); data.put\("content", content\);

JobDetail job = JobBuilder.newJob\(PushAppJob.class\) .withIdentity\(\[name\], \[group\]\) .setJobData\(data\).build\(\);

//基本触发器 Trigger trigger = TriggerBuilder.newTrigger\(\).startAt\(\[StartTime\]\) .endAt\(\[EndTime\]\) .withSchedule\(DailyTimeIntervalScheduleBuilder.dailyTimeIntervalSchedule\(\) .withInterval\(\[间隔类别\], \[时间间隔\]\)\) .withIdentity\(\[name\], \[group\]\).build\(\);

//Cron表达式触发器 CronScheduleBuilder cronScheduleBuilder = CronScheduleBuilder.cronSchedule\(\[cron表达式\]\); CronTrigger cronTrigger = TriggerBuilder.newTrigger\(\).startAt\(\[StartTime\]\) .endAt\(\[EndTime\]\).withSchedule\(cronScheduleBuilder\) .withIdentity\(\[name\], \[group\]\).build\(\);

//提交计划任务 scheduler.scheduleJob\(job, trigger\); &lt;/pre&gt;

* 任务（Job）

  ```text

  //未注入此Bean
  public class PushAppJob implements Job {

    使用Setter对应JobDataMap中的参数
    private @Setter From from;
    private @Setter Target target;
    private @Setter AppContent content;

    @Override
    public void execute(JobExecutionContext context) throws JobExecutionException {
        //由于未注入该Bean，使用此方法调用其他已注入Service
        //TestService service = SpringUtils.getBean(TestService.class);

        System.out.println("-----样例任务----");
    }
  }
  ```

【监听器】

* 继承SchedulerListenerSupport并注入后即可增加监听器,亦可选择实现SchedulerListener

  ```text

  @Order(1)
  @Component
  public class FinishScheduleListener extends SchedulerListenerSupport {

    //样例，在Trigger最后一次执行的时候监听
    @Override
    public void triggerFinalized(Trigger trigger) {
        System.out.println("-------正常结束-------");
    }
  }
  ```
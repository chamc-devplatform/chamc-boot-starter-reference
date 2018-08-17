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
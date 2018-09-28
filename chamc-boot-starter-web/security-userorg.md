# 用户数据同步

## 前置条件

新建以下7张表：t\_sys\_org，t\_sys\_permission，t\_sys\_role，t\_sys\_role\_permission，t\_sys\_user，t\_sys\_user\_org，t\_sys\_user\_role。在jar包中获取建表脚本:chamc-boot-starter-web.jar -> src/main/resources/sql -> \`init\_sys_\[mysql\|oracle\].sql\`

## 同步程序设计说明

* 数据源来自新版人力系统`EntUserDb`,目标数据库是业务系统数据库

*  同步模块可通过配置chamc.web.sync.enable开启或关闭，如下“使用demo”中的配置项所示；
*  同步任务既可以直接配置定时任务，也可以直接通过service服务调用
*  同步程序使用多线程处理，相对来说效率较高，线程池可配置，如下“使用demo”中的配置项所示，多线程处理后会等待处理结果，然后再继续执行下一步逻辑；
* 同步程序可以添加干预listener，只需要继承`SyncOperationListenerAdapter`或者实现`SyncOperationListener`即可；
* 同步按照`机构 -> 用户 -> 用户机构`或者`用户 -> 机构 -> 用户机构`的顺序执行

## 使用

### 添加配置项

在项目的`application.propertis`中添加配置项，如下所示：

注：集群模式下的配置请参考[定时任务](chamc-boot-starter-web/timer.md)

    chamc.security.permission.enable=true   //启用安全
    chamc.web.sync.operatorId=1 # 同步操作人的用户id
    #chamc.quartz.store-type=jdbc   #集群模式下，需要配置
    #是否启用定时同步，如下配置或chamc.web.sync.timer.enable=true
    chamc.web.sync.timer.enable=true
    #cron为定时计划表达式，默认值为0 0 21 0 * *，即每天晚上九点运行
    chamc.web.sync.timer.cron=0 0 21 0 * *
    
注2：EntUserDb配置：目前只有两个EntUserDb数据库，一个是仿真环境的，一个是生产环境的；
    * 默认不做任何配置的情况下，会根据服务所在网段进行环境识别，只有"10.80.36"和"10.80.40"网段下会被识别为生产环境，同步数据时会同步生产环境数据，其他网段都会同步仿真环境数据；
     * 可以通过如下配置指定EntUserDb配置数据源（数据源配置请联系[罗明强](mailto:luomingqiang@chamc.com.cn)）。
     
    chamc.web.sync.entuserdb.url=xxxxx
    chamc.web.sync.entuserdb.username=xxxx
    chamc.web.sync.entuserdb.password=xxxx
     

### 运行同步程序

如果添加配置项的时候，配置了启用定时同步任务（`chamc.web.sync.timer.enable=true`或`chamc.web.permission.sync.timer.enable=true`），则会自动按照定时任务计划（`cron`）运行同步程序，不需要再添加其他代码调用。

### 同步接口调用

如果需要手动调用同步程序，只需要在业务系统代码中注入对应的`syncService`并调用同步方法即可，如下所示：

    @RestController
    public class SyncController {
    
        private @Autowired com.chamc.boot.web.support.sys.service.SyncService service;
    
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
    
    }

如果需要对同步程序进行干预，可采用以下方式添加bean并注入到系统中，可以有多个listener共存，共同起作用：

* 方法1：继承`SyncOperationListenerAdapter`实现需要的方法
* 方法2：继承`SyncOperationListener`并实现其中的方法

示例：

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
            if(user.getAccount().equals("test@test.com")) {
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

## cron简单示例

ps：可网上在线搜索“cron在线表达式”，在线获得表达式，粘贴使用即可，另[cron说明直通车](http://blog.csdn.net/u011116672/article/details/52517247)

cron表达式为6位的、用空格分隔的字符串

    0 0 21 * * *

如上示例，6位的表达式，分别为`second minute hour day month weekday`；

<table>
   <tr>
      <td>类别</td>
      <td>是否必须</td>
      <td>值范围</td>
      <td>可用符号</td>
      <td>附注</td>
   </tr>
   <tr>
      <td>second</td>
      <td>是</td>
      <td>0-59</td>
      <td>`*` `,` `-` `\`</td>
      <td>`\`表示每隔多久执行</td>
   </tr>
   <tr>
      <td>minute</td>
      <td>是</td>
      <td>0-59</td>
      <td>`*` `,` `-` `\`</td>
      <td>`*`表示通配任意匹配，`,`表示可以有多个值，用`,`隔开，`-`表示一个区间范围</td>
   </tr>
   <tr>
      <td>hour</td>
      <td>是</td>
      <td>0-23</td>
      <td>`*` `,` `-` `\`</td>
      <td></td>
   </tr>
   <tr>
      <td>day</td>
      <td>是</td>
      <td>1-31</td>
      <td>`*` `,` `-` `\` `?`</td>
      <td>`?`表示当同时配置了星期时，与星期互斥，即是这一天但不是星期n的时候，在这一天执行</td>
   </tr>
   <tr>
      <td>month</td>
      <td>是</td>
      <td>1-12/JAN-DEC</td>
      <td>`*` `,` `-` `\`</td>
      <td></td>
   </tr>
   <tr>
      <td>weekday</td>
      <td>是</td>
      <td>1-7/SUN-SAT</td>
      <td>`*` `,` `-` `\` `?`</td>
      <td>1表示星期一</td>
   </tr>
</table>

示例：

`0 0 21  __ _`：表示每天晚上九点整执行任务  
`\30 0 21_   __`：表示每天晚上九点的第一分钟，每隔30秒执行一次任务  
`0 0-30 21,22  __ \*`:表示每天晚上九点到九点半、十点到十点半都执行任务

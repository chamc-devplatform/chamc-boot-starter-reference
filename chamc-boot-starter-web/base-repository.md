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
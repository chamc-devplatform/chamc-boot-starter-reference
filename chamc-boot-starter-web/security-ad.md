# ad域登陆

## 说明

如下图所示，域登陆的处理流程：

> - 客户端向应用系统发送业务请求  
> - 业务系统检测是否登录：
>   - 已登录：正常请求
>   - 未登录：
>       - json请求：返回401状态码
>       - 普通请求：直接重定向到域登陆地址
> - 如果是json请求，则客户端收到401错误信息
>   - 客户端向应用系统请求ad登录地址
>   - 应用系统响应ad登录地址（通过配置的参数拼接登录地址，然后返回客户端）
> - 跳转ad登录页面
> - ad登录（默认不弹登录框并自动使用win电脑域账户信息，可通过ie设置让弹出登录框）
>   - ad登录失败：继续弹出登录框
>   - ad登录成功，根据retUrl返回并附上userInfo参数
> - 在retUrl对应的页面或请求中，向后端请求用户登录验证
> - 用户登录，将信息塞入session，然后根据returnType做页面跳转
> - 返回

![域登陆](https://i.imgur.com/iOSp5Kp.png)

## 使用

1、使用域登陆首先要开启web模块的security，并加必要的配置，如ad登录地址、应用名称等。

2、域登陆的控制需要与对权限的配置相结合使用，权限控制相关功能可参考[安全-权限控制](chamc-boot-starter-web/security-permission.md )

3、对于登录类型（login-type），默认配置为combine、也支持配置为ajax或者是normal，登录类型在域登陆前（未登录 -> 域登陆 -> 业务系统登录验证 -> 成功登录）起作用
- login-type如果是ajax，返回401 json错误
- 组合（combine）的话，根据请求类型返回，ajax请求返回401 json错误，普通请求直接重定向到ad登录页面

4、域登陆成功后到业务做登录验证，并根据returnType参数控制页面跳转
- successurl：返回配置的成功页面，即chamc.security.ad.success-url=xxx的地址
- originurl：返回登陆前的页面

5、域服务器登录成功后，会根据retUrl重定向到业务系统做登录验证，web模块提供了`/login`和`/ajaxLogin`的mapping，所需参数为域登陆成功后自动附加到retUrl上的参数。

### 配置

    #开启安全权限控制
    chamc.security.enable=true
    #ad域服务器的地址
    chamc.security.ad.url=  
    #本系统申请的在域登陆服务器中对应的应用名称，该app-name在域登陆的时候会作为参数传过去  
    chamc.security.ad.app-name=testApp
    #域登陆成功后，重定向回业务系统的url，然后在该url对应的页面请求后端进行登录验证，登录验证结束再根据returnType做页面跳转
    chamc.security.ad.ret-url=
    #login-type:ajax normal combine，不配置的话为combine，登录类型的说明如上第三点所述
    chamc.security.ad.login-type=ajax
    #return-type:success-url origin-url，如上说明第四点所述
    chamc.security.ad.return-type=success-url

    #是否支持同一账户多处登录
    chamc.security.ad.multi-login=false

    #业务系统登陆成功后，重定向的url，该配置在return-type=success-url时生效
    chamc.security.ad.success-url=

### 使用示例1

如上配置说明所示，添加必要的配置后，系统启动后会自动判断是否登录，如果没有登录会自动做域登陆，并根据配置的ret-url自动跳转

### 使用示例2

个性化登录：可以在登录的时候对LoginUser做个性化处理，使用方式如下：

     public class LoginCustomizer implements LoginUesrCustomizer {
    
    	@Override
    	public void customize(LoginUser loginUser) {
    
    	}
    }
    
    @Configuration
    public class TestAutoConfig {
    	
    	@Bean
    	public LoginUesrCustomizer loginCustomizer() {
    		return new LoginCustomizer();
    	}
    }
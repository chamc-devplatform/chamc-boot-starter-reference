# ad域登陆

## 说明

如下图所示，域登陆的处理流程：

> - 客户端向应用系统发送业务请求  
> - 业务系统检测是否登录：
>   - 已登录：正常请求
>   - 未登录：返回401状态码
> - 客户端收到401错误信息
> - 客户端向应用系统请求ad登录地址
> - 应用系统响应ad登录地址（通过配置的参数拼接登录地址，然后返回客户端）
> - 客户端根据ad登录地址跳转
> - ad登录（默认不弹登录框并自动使用win电脑域账户信息，可通过ie设置让弹出登录框）
>   - ad登录失败：继续弹出登录框
>   - ad登录成功，返回retUrl+userInfo参数
> - 在retUrl对应的页面或请求中，处理userInfo并做后续登录及用户信息加载，将登录信息放入session中
> - 登录成功

![域登陆](https://i.imgur.com/JrH2Ls2.png)

## 使用

使用域登陆首先要开启web模块的security，并加必要的配置，如ad登录地址、应用名称等。

域登陆的控制需要与对权限的配置相结合使用，权限控制相关功能可参考[安全-权限控制](chamc-boot-starter-web/security-permission.md )

对于登录类型（login-type），默认配置为combine、也支持配置为ajax或者是normal，他们的区别是：登录失败的时候，如果login-type=ajax，会抛出401异常，如果login-type是其他的话会跳转错误页面

域服务器登录成功后，会根据retUrl重定向到业务系统做登录，web模块提供了`/login`和`/ajaxLogin`的mapping，参数`userInfo58914`为域登陆成功后自动附加到retUrl上的参数。

域登陆成功后，如果配置了return-type为sucess-url，那么业务系统登录成功后会重定向到success-url。


### 配置

    chamc.security.enable=true
    #本系统申请的在域登陆服务器中对应的应用名称，该app-name在域登陆的时候会作为参数传过去    
    chamc.security.ad.app-name=testApp
    #login-type:ajax normal combine，不配置的话为combine
    chamc.security.ad.login-type=ajax
    #return-type:success-url origin-url
    #业务登陆成功后的重定向url的类型，是根据配置的success-url或者是normal（默认返回null）
    chamc.security.ad.return-type=success-url
    #域登陆成功后，重定向的url
    chamc.security.ad.ret-url=
    #是否支持统一账户多处登录
    chamc.security.ad.multi-login=false
    #ad域服务器的地址
    chamc.security.ad.url=
    #业务系统登陆成功后，重定向的url，该配置在return-type=success-url时生效
    chamc.security.ad.success-url=

### 使用示例1

如上配置说明所示，添加必要的配置后，系统启动后会自动判断是否登录，如果没有登录会自动做域登陆，并根据配置的ret-url自动跳转

### 使用示例2

自己写登录，需要

- 1、写一个类继承`UserDetails`并实现必要的方法，该类对应用户信息，包括权限等
- 2、写一个类继承`UserDetailsService`并实现必要的方法，然后将该类注册为bean

示例：

    public class CustomUserDetail implements UserDetails {
    
    	private static final long serialVersionUID = 8032759389073091490L;
    
    	@Override
    	public Collection<? extends GrantedAuthority> getAuthorities() {
    		return null;
    	}
    
        ......
    }
    
    public class CustomUserDetailsService implements UserDetailsService {
    
    	@Override
    	public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
    		return null;
    	}
    
    }
    
    @Configuration
    public class TestAutoConfig {
    	
    	@Bean 
    	public UserDetailsService customUserDetailsService() {
    		return new CustomUserDetailsService();
    	}
    	
    }
# 登陆

## 说明

目前登录模块提供AD登录功能、SSOToken解析登录功能，并为用户名密码等自定义登录方式提供统一配置以及代码样例支持。下面将分别对两种登录方式的处理逻辑、登录模块的配置以及自定义登录方式的样例代码等进行介绍。

## AD登录
AD登录的处理逻辑如下图所示：

![](https://i.imgur.com/A6WNhM9.png)

## SSOToken解析
SSOToken解析登录的处理逻辑如下图所示：

![](https://i.imgur.com/2uVhWKW.png)

## 登录模块配置

1.AD必要配置

有了如下配置，就可以进行AD登录，但是仅支持页面请求登录类型，并且登录成功返回登录请求前页面的操作逻辑。

	#开启安全权限控制
	chamc.security.enable=true
	#AD域服务器地址
	chamc.security.ad.url=XXXX
	#本系统申请的在域登陆服务器中对应的应用名称，该app-name在域登陆的时候会作为参数传过去
	chamc.security.ad.app-name=XXX

2.AD登录其他配置
	
如果想处理返回Json数据的Ajax请求的登录，需要增加如下配置。
	
	#Ajax请求的AD登录地址
    chamc.security.ad.ret-url=XXX

如果想处理返回页面的Ajax请求的登录，需要增加如下配置，**配置的地址需要以"/"开头**。

	#Ajax请求，登录成功后返回的地址，默认为"/"
	chamc.security.ad.success-url=xxx

3.SSOToken解析配置

有了如下配置，就可以进行SSOToken解析登录。

	#实际上该配置默认配置为true，即开启登录模块无需做任何配置就可以使用SSOToken解析功能模块，可以将如下配置设置为false，显示的关闭该功能。
	security.sso.enable-token-filter=true

4.关于用户登录次数限制的配置

可以通过如下配置限制用户的登录次数，实现逻辑为后面的登录踢掉前面的登录。
	
	#可以通过如下配置配置同一用户最大允许登录次数为N，默认配置为用户允许最大登录次数为1
	chamc.security.maximum-sessions=N

## 自定义登录方式

目前登录模块支持对系统自定义的其他登录方式（如用户名密码登录等方式）的统一配置，只需要实现如下两个模块的代码就可以实现自定义登录方式。

1）拦截处理登录请求的filter

2）获取用户身份信息的UserDetailsLoader

**下面以邮箱密码登录为例，介绍一下自定义登录方式的样例代码：**

1）定义拦截处理邮箱密码登录的filter,并**继承web模块的AbstractAuthenticationFilter**：

	public class EmailPasswordLoginFilter extends AbstractAuthenticationFilter {

	public EmailPasswordLoginFilter() {
		super(new AntPathRequestMatcher("/sys/login"));//配置拦截的登录请求URI,这里登录请求为"/sys/login"
		AuthenticationSuccessHandler successHandler = this.getSuccessHandler();
		if (successHandler instanceof SavedRequestAwareAuthenticationSuccessHandler) {
			((SavedRequestAwareAuthenticationSuccessHandler) successHandler).setDefaultTargetUrl("/sys/index");
		}//配置登录成功后的返回地址，如果不配置则默认为登录请求前的地址，这里配置登录成功后返回到"/sys/index"
	}
	
	//重写该方法，获取你需要构造身份信息的参数并构造身份信息token，这里获取“email”和“password”构造身份信息
	@Override
	protected Authentication buildAuthentication(HttpServletRequest request, HttpServletResponse response)
			throws AuthenticationException, IOException, ServletException {
		String email = request.getParameter("email");
		String password = request.getParameter("password");
		return new UsernamePasswordAuthenticationToken(email, password);
	}
	
}

2）定义获取身份信息的UserDetailsLoader，并**继承web模块的AbstractUserDetailsLoader**：

	public class EmailUserDetailsLoader extends AbstractUserDetailsLoader {

	public EmailUserDetailsLoader() {
		super(new AntPathRequestMatcher("/sys/login"));//配置该UserDetailsLoader对应的登录请求，这里对应的登录请求为登录请求为"/sys/login"
	}

	@Override
	public UserDetails loadUserByUsername(String username) {
		//这里构造 UserDetails并将其返回
	}
	
}

3）将定义的filter和UserDetailsLoader以Bean的形式注册到Spring容器中，**注意给UserDetailsLoader一个order**。

例如将（1）和（2）中定义的EmailPasswordLoginFilter和EmailUserDetailsLoader以Bean的形式注册到Spring容器中的的代码如下：

    @Configuration
    public class TestAutoConfig {
    	
		public @Bean AbstractAuthenticationFilter emailPasswordLoginFilter() {
		return new EmailPasswordLoginFilter();
		}

		@Order(1)
		public @Bean IUserDetailsLoader emailUserDetailsLoader() {
		return new EmailUserDetailsLoader();
		}
    }


通过以上三个步骤，就可以实现自定义的登录方式，并且用户登录次数限制的等配置对该登录方式同样有效。

## 登录用户个性化处理

目前登录模块提供了对登录用户的个性化处理接口，可以通过实现LoginUesrCustomizer接口中的customize（）方法实现对登录用户进行个性化处理，使用方法如下：

（1）实现个性化处理逻辑：

     public class LoginCustomizer implements LoginUesrCustomizer {
    
    	@Override
    	public void customize(LoginUser loginUser) {
    		//登录用户个性化处理操作
    	}
    }

（2）将个性化处理类以Bean的形式注册到Spring容器中。
    
    @Configuration
    public class TestAutoConfig {
    	
    	@Bean
    	public LoginUesrCustomizer loginCustomizer() {
    		return new LoginCustomizer();
    	}
    }

# 登录

## 说明

目前登录模块提供AD登录功能、SSOToken解析登录功能，并为用户名密码等自定义登录方式提供统一配置以及代码样例支持。下面将分别对两种登录方式的处理逻辑、登录模块的配置以及自定义登录方式的样例代码等进行介绍。

## AD登录

AD登录模块的功能主要包括对未登录请求的处理，登录认证过程以及登录成功后的处理。

1.**未登录请求的处理**

对于未登录的请求，分别对Ajax请求和页面请求两种类型的请求做出处理，处理流程如下：

1）Ajax请求

当Ajax请求发现未登录时，系统将返回Json格式的401错误，其处理流程如下：

![](https://i.imgur.com/IDM3m6m.jpg)

2）页面请求

当页面请求发现未登录时，系统将默认去做登录跳转，其处理流程如下：

（1）首先判断是否配置了loginUrl，如果已配置，则跳转到配置的跳转页面；

（2）如果没有配置了loginUrl，则直接跳转到Ad获取用户信息。

![](https://i.imgur.com/BKfJ4tB.jpg)

2.**登录认证过程**：

对于登录认证请求，我们针对页面请求和Ajax请求提供了两个filter处理两种情况的登录认证。

1）对于页面请求，会请求“/login”处理认证身份认证，其处理流程如下：

（1）AdLoginFilter负责拦截过滤“/login”的登录请求，提取用户信息,以及在身份信息验证成功后将身份信息存入session等。

（2）SysUserDetailsLoader负责从数据表t_sys_user中查询用户信息，构建并返回UserDetails。

![](https://i.imgur.com/zjnW4iu.jpg)

2）对于Ajax请求，需要请求“/ajaxLogin”处理身份认证，我们提供了AdAjaxLoginFilter处理认证流程，除了请求地址是“/ajaxLogin”不同于页面的“/login”之外，其他具体的处理流程和页面请求相似。

**3.登录成功后的处理**

对于登录成功后的处理，同样会分为页面请求的处理和Ajax请求的处理，对于页面请求，会返回页面，对于Ajax请求，会返回Json格式的OK。

1）页面请求，其具体处理流程如上图所示

- 如果登录前无请求或者登陆前请求类型是Ajax请求，会重定向到successUrl.
- 否则，会重定向到登陆前请求地址。

2）Ajax请求，登录请求直接返回Json格式的OK。

## SSOToken解析
SSOToken解析登录的处理逻辑如下图所示：

![](https://i.imgur.com/2kt2Ryl.jpg)

## 登录模块配置

1.AD必要配置

有了如下配置，就可以进行AD登录，但是仅支持页面请求登录类型，并且默认的successUrl为“/”

	#开启安全权限控制
	chamc.security.enable=true
	#AD域服务器地址
	chamc.security.ad.url=XXXX
	#本系统申请的在域登陆服务器中对应的应用名称，该app-name在域登陆的时候会作为参数传过去
	chamc.security.ad.app-name=XXX

2.AD登录其他配置
	
如果想处理返回Json数据的Ajax请求的登录，需要增加如下配置，并需要业务系统自行处理访问地址的拼接和处理逻辑。
	
	#Ajax请求的AD登录地址
    chamc.security.ad.ret-url=XXX

如果想配置自己的successUrl，**配置的地址需要以"/"开头**。

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

5.关于自定义进行AD登录而仅使用自定义登录方式的配置

	#可以通过配置loginUrl地址，而控制系统直接跳转带到配置的登录地址进行登录而不进行AD登录。
	chamc.security.login-url=xxx

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

**注意：这里的UserDetails获取需要用户自己建立一张登录信息表，如果需要设置密码等信息不可以直接修改t_sys_user表中的密码字段。**

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

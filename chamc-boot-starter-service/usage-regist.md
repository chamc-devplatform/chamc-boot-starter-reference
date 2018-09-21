# 服务注册

## 添加依赖

在pom.xml文件中引入chamc-boot-starter-service的依赖：

```
<dependency>
	<groupId>com.chamc.boot</groupId>
	<artifactId>chamc-boot-starter-service</artifactId>
	<version>0.0.1-SNAPSHOT</version>
</dependency>
```

## 配置文件配置

在application.properties文件中添加配置：

	#自定义应用名
	spring.application.name=
	#应用对应token
	chamc.service.registry.token=

如果需要使用注册中心调用其他服务，且服务与注册中心使用同一token，token配置项可配置为

	#应用对应token
	chamc.service.token=

这样你的应用就注册到了注册中心，其他应用可以通过注册中心发现你的服务，从而调用你的服务。

## 注意

* token获取：请到服务管理系统（请联系[唐红石](mailto:tanghongshi@chamc.com.cn)获取地址）申请。
* 注册中心是根据网段（开发、测试、仿真、生产）来判断的，若应用服务器不在相应的网段上，需要手动配置（否则，注册到开发环境上），在配置文件`application.properties`中添加：

		eureka.client.service-url.defaultZone=http://XXXXX:8761/eureka,http://XXXXX:8761/eureka

如需配置地址请联系[唐红石](mailto:tanghongshi@chamc.com.cn)。

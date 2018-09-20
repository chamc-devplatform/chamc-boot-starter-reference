## 简介 ## 

该组件提供对各层（controller、service、repository）进行日志跟踪的功能，详细使用方法如下。 

## 配置 ##

在application.properties文件中开启日志跟踪：

    chamc.tracelog.enable=true 

请求某个接口后，打印日志如下： 
	2017-12-27 16:12:07.608 TRACE 988 --- [nio-8080-exec-9] c.c.b.w.config.log.TraceLogInterceptor : Entering UserController.bookOrUsers(Long) 2017-12-27 16:12:07.612 TRACE 988 --- [nio-8080-exec-9] c.c.b.w.config.log.TraceLogInterceptor : Entering UserService.findBooks() 
	2017-12-27 16:12:07.620 TRACE 988 --- [nio-8080-exec-9] c.c.b.w.config.log.TraceLogInterceptor : Entering .Proxy192.findAll() 
	Hibernate: select book0_.id as id1_4_, book0_.name as name2_4_, book0_.price as price3_4_, book0_.writer as writer4_4_ from t_book book0_ 
	2017-12-27 16:12:07.714 TRACE 988 --- [nio-8080-exec-9] c.c.b.w.config.log.TraceLogInterceptor : Leaving .Proxy192.findAll, took 94ms 2017-12-27 16:12:07.714 TRACE 988 --- [nio-8080-exec-9] c.c.b.w.config.log.TraceLogInterceptor : Leaving UserService.findBooks, took 102ms 2017-12-27 16:12:07.714 TRACE 988 --- [nio-8080-exec-9] c.c.b.w.config.log.TraceLogInterceptor : Leaving UserController.bookOrUsers, took 106ms 

同时可以对各层进行配置： 
![](https://i.imgur.com/8e6mqVi.png)


	#是否开启，默认false。其中XXXX代表controller、service或repository 
    chamc.tracelog.XXXX.enable
	#是否打印Enter日志 ，默认true
	chamc.tracelog.XXXX.showEnter 
	#是否打印Exit日志
	chamc.tracelog.XXXX.showExit
	#日志内容是否包含类名，默认true
	chamc.tracelog.XXXX.className
	#日志内容是否包含方法名，默认true
	chamc.tracelog.XXXX.methodName
	#Enter日志内容是否包含参数值，谨慎开启，可能会影响性能 ，默认false
	chamc.tracelog.XXXX.arguments
	#Enter日志内容是否包含参数类型，默认true
	chamc.tracelog.XXXX.argumentTypes
	#Exit日志内容是否包含返回值，谨慎开启，肯会影响性能 ，默认false
	chamc.tracelog.XXXX.returnValue
	#Exit日志是否包含执行时间，单位：ms ，默认true
	chamc.tracelog.XXXX.invocationTime
	#日志切入点
	chamc.tracelog.XXXX.pointcut
	


## demo ##

在application.properties配置如图所示： 

    chamc.tracelog.enable=true 
    chamc.tracelog.controller.enable=true 
    chamc.tracelog.service.enable=true 
    chamc.tracelog.service.return-value=true 
    chamc.tracelog.repository.enable=true 
    chamc.tracelog.repository.arguments=true 

请求一个接口，service的返回值和repository的入参被跟踪打印，如下： 

    2017-12-27 16:22:33.064 TRACE 988 --- [io-8080-exec-10] c.c.b.w.config.log.TraceLogInterceptor : Entering UserController.bookOrUsers(Long) 2017-12-27 16:22:33.077 TRACE 988 --- [io-8080-exec-10] c.c.b.w.config.log.TraceLogInterceptor : Entering UserService.findBooks()
	2017-12-27 16:22:33.086 TRACE 988 --- [io-8080-exec-10] c.c.b.w.config.log.TraceLogInterceptor : Entering .Proxy202.findAll()
	2017-12-27 16:22:33.129 INFO 988 --- [io-8080-exec-10] o.h.h.i.QueryTranslatorFactoryInitiator : HHH000397: Using ASTQueryTranslatorFactory Hibernate: select book0_.id as id1_4_, book0_.name as name2_4_, book0_.price as price3_4_, book0_.writer as writer4_4_ from t_book book0_ 
	2017-12-27 16:22:33.135 TRACE 988 --- [io-8080-exec-10] c.c.b.w.config.log.TraceLogInterceptor : Leaving .Proxy202.findAll(), took 49ms 2017-12-27 16:22:33.135 TRACE 988 --- [io-8080-exec-10] c.c.b.w.config.log.TraceLogInterceptor : Leaving UserService.findBooks with return value [Book(id=1, name=三国演义, price=56, writer=罗贯中), Book(id=2, name=测试书本, price=12, writer=测试)], took 58ms 
	2017-12-27 16:22:33.135 TRACE 988 --- [io-8080-exec-10] c.c.b.w.config.log.TraceLogInterceptor : Leaving UserController.bookOrUsers, took 71ms




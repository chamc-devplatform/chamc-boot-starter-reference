
###定时任务模块

####【框架简介及术语定义】

> - 定时任务模块使用`Quartz`框架，`Quartz` 是任务调度领域的一个开源项目，完全基于 Java 实现。

**（1）Quartz 特点：**

- 强大的调度功能，支持多样的调度方法。
- 灵活的应用方式，支持任务和调度的多种组合方式，支持调度数据的多种存储方式。
- 分布式和集群能力。

**（2）quartz调度核心元素**

- **每个任务包含**：一个任务实现类（Job的实现），一个根据任务实现描述实际任务信息的类（JobDetail），一个触发器（Trigger），可有执行参数（JobDataMap）、监听器，被一个任务调度器管理（Scheduler）。

- **Job**：是一个接口，只有一个方法`void execute(JobExecutionContext context)`,开发者实现该接口以定义运行任务，`JobExecutionContext`类提供了调度上下文的各种信息。Job运行时的信息保存在`JobDataMap`实例中。

- **JobDataMap**：可用于保存任何您希望在执行时对作业实例可用的数据对象。 `JobDataMap`是Java Map接口的一个实现，并且具有用于存储和检索原始类型的数据的一些额外的便利方法。

- **JobDetail**：用来描述Job实现类及其它相关的静态信息，如Job名字、关联监听器等信息。

- **Trigger**：触发器，用于定义任务调度的时间规则，有`SimpleTrigger`，`CronTrigger`等。
	- `SimpleTrigger`可支持仅触发一次或者具有开始结束时间且以固定时间间隔周期执行的任务。
	- 而`CronTrigger`则可以通过`Cron`表达式定义出复杂时间规则的调度方案，一般用于无开始结束时间的周期性任务，也可用于精确定义仅执行一次的调度任务。
</br>
- **Scheduler**：任务调度器，是实际执行任务调度的控制器，需要时注入即可。代表一个`Quartz`的独立运行容器，`Trigger`和`JobDetail`可以注册到`Scheduler`中，作为查找定位容器中某一对象的依据。

- **线程池**：Scheduler使用一个线程池作为任务运行的基础设施，任务通过共享线程池中的线程提高运行效率，可在配置文件修改相关配置。

> 更多细节请参考：[Quartz 2.2官方教程](http://www.quartz-scheduler.org/documentation/quartz-2.2.x/tutorials/ "Quartz 2.2官方教程")

####【定时任务模块设计说明】

- 定时任务模块使用开源作业调度框架`Quartz`实现，简化业务系统配置和使用。
- 业务系统可以方便地添加任务，并使用`Quartz`管理用户组织机构同步任务。
- 默认使用内存单应用模式，无需业务系统在数据库建表，可配置为使用数据库和集群模式。
- 提供`Quartz`默认配置，业务系统可在配置文件中覆盖。
- 提供对`Scheduler`注入监听器的功能。

####【配置】

**（1）Quartz相关配置说明**

> - 存储方式分为内存存储和数据库存储两种方式
	- 内存存储 ： Quartz在服务启动时在内存中存储调度任务，重启应用会丢失，不支持多实例部署。
	- 数据库存储 : 使用开发平台jar包内的建表sql建立Quartz相关的数据表，可实现任务持久化，重启应用不会丢失且继续执行，支持多实例部署。
> - 使用数据库存储时需要建表，建表sql位置为：用压缩文件打开maven仓库`.m2\repository\com\chamc\boot\chamc-boot-starter-web\0.0.1-SNAPSHOT`文件夹下的开发平台jar包，在`/sql/quartz`文件夹下找到建表sql，如mysql数据库为`tables_mysql_innodb.sql`。
> - 多实例情况下**必须**使用集群+JDBC模式,集群模式下**必须且默认**使用数据库存储
> - 集群所有节点应使用时间同步服务，时间误差应在**1s内**

	# Quartz模式  单应用(MONOMER)、集群(CLUSTER)
	chamc.quartz.mode=monomer
	# 存储模式   内存(MEMORY)、数据库(JDBC)
	chamc.quartz.store-type=jdbc
	#（可选）Quartz数据库名称，使用Spring数据源的时候不用配置，在使用多数据源时默认使用缺省数据源，可以指定为任意数据源名称
	chamc.quartz.data-source-name=test
	#（可选）覆盖默认配置样例，其他可覆盖配置详见下面配置文件
	# 覆盖方式为“chamc.quartz.properties” 加 Quartz配置
	chamc.quartz.properties.org.quartz.scheduler.instanceName=ChamcQuartzScheduler
	
**（2）可支持覆盖的Quartz配置及默认值**

> - 一般情况下不需要覆盖Quartz的默认配置，可跳过此小节
> - 支持覆盖的配置包括线程池相关配置、数据库表前缀配置和错失时间等，如需覆盖Quartz的其他配置请与开发平台联系。
	
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

####【使用】

**（1）简易使用——直接注入任务和对应触发器**

> - 定时任务模块对注入的`Trigger`和对应的`Job`进行提交，因此`Trigger`注入时**必须使用`.forJob()`方法**
> - 任务类应实现`Job`或继承`QuartzJobBean`
> - 代码示例中被中括号`[  ]`包裹的内容为占位说明，请根据业务替换，其中Trigger和Job建议设置名称(name) 和分组(group)作为唯一标识。

	// 注入任务Bean，storeDurably()使任务在没有对应触发器时不会被删除
	// Job建议设置名称和组 .withIdentity([name], [group])
	public @Bean JobDetail demoJob() {
		return JobBuilder.newJob(SampleJob.class).storeDurably().build();
	}
	
	// 注入触发器Bean，使用forJob与任务关联
	// Trigger建议设置名称和组 .withIdentity([name], [group])
	public @Bean Trigger trigger() {
		return TriggerBuilder.newTrigger().forJob(demoJob()).startNow()
			.withSchedule(DailyTimeIntervalScheduleBuilder.dailyTimeIntervalSchedule().withIntervalInSeconds(5))
			.withIdentity([name], [group]).build();
	}
	
	// 任务类	
	@Slf4j
	public static class SampleJob extends QuartzJobBean {
	
		@Override
		protected void executeInternal(JobExecutionContext arg0) throws JobExecutionException {
			log.info("样例任务");
		}
	
	}

**（2）基本使用——使用`JobDataMap`传参、更多触发器种类**

> - 触发器构建与启动计划任务

	// 注入Scheduler
	private @Autowired Scheduler scheduler;
	
	JobDataMap data = new JobDataMap();
	// 示例内容，对应下面Job类中的属性
	data.put("from", from);
	data.put("target", target);
	data.put("content", content);
	
	JobDetail job = JobBuilder.newJob(PushAppJob.class)
			.withIdentity([name], [group])
			.setJobData(data).build();
	
	// 基本触发器
	Trigger trigger = TriggerBuilder.newTrigger().startAt([StartTime])
			.endAt([EndTime])
			.withSchedule(DailyTimeIntervalScheduleBuilder.dailyTimeIntervalSchedule()
				.withInterval([间隔类别], [时间间隔]))
			.withIdentity([name], [group]).build();
	
	// Cron表达式触发器
	CronScheduleBuilder cronScheduleBuilder = CronScheduleBuilder.cronSchedule([cron表达式]);
	CronTrigger cronTrigger = TriggerBuilder.newTrigger().startAt([StartTime])
			.endAt([EndTime]).withSchedule(cronScheduleBuilder)
			.withIdentity([name], [group]).build();
	
	// 提交计划任务
	scheduler.scheduleJob(job, trigger);

> - 任务（Job）

	// 未注入此Bean
	public class PushAppJob implements Job {
		
		使用Setter对应JobDataMap中的参数
		private @Setter From from;
		private @Setter Target target;
		private @Setter AppContent content;
	
		@Override
		public void execute(JobExecutionContext context) throws JobExecutionException {
			// 由于未注入该Bean，使用此方法调用其他已注入Service
			//TestService service = SpringUtils.getBean(TestService.class);
			
			System.out.println("-----样例任务----");
		}
	}

####【监听器】

> - 继承`SchedulerListenerSupport`并注入后即可增加监听器,亦可选择实现`SchedulerListener`。
> - `SchedulerListenerSupport`提供了大量的可覆盖监听器，下面示例为Trigger最后一次执行时的监听器示例。

	@Order(1)
	@Component
	public class FinishScheduleListener extends SchedulerListenerSupport<T> {
	
		// 样例，在Trigger最后一次执行的时候监听
		@Override
		public void triggerFinalized(Trigger trigger) {
			System.out.println("-------正常结束-------");
		}
	}

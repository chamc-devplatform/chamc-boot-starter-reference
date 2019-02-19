#分布式锁
##1 简介

目前很多系统及应用都是分布式部署的，分布式场景中(多实例)的数据一致性问题一直是一个比较重要的话题。基于各基础系统和业务系统对分布式锁和方法幂等的需要，后端框架实现了统一的分布式锁功能，希望能够提供稳定易用且便于扩展的服务。

【支持的功能】

- 可以保证在分布式部署的应用集群中，同一个方法在同一时间只能被一台机器上的一个线程执行。
- 这把锁要是一把可重入锁，允许相同线程对锁进行重入（避免死锁）
- 可以是一把阻塞锁(暂不支持公平锁)
- 高可用的获取锁和释放锁功能，确保性能
- 支持锁的过期时间、获取等待时间等

##2 准备

目前后端框架使用注解和切面实现分布式锁功能，使用时只需在方法上配置分布式锁注解即可。获取和释放分布式锁的方法通过接口暴露，可支持多种实现，**目前仅支持使用Redis实现**。

【注解】

使用自定义注解`@DistributedLock`进行配置。可在`Controller`方法上进行配置，具有此注解的方法会通过Redis分布式锁控制并发访问。默认为非阻塞模式，仅尝试获取一次，默认在方法返回后立即释放分布式锁。

【可配置内容】

- `key`: **无默认值，必须配置**，可通过SPEL表达式进行配置。例如lockKey = "#param.id"；支持通过SPEL表达式调用方法。
- `leaseTime`：分布式锁的过期时间。单位为毫秒，默认为-1，即不会过期。
- `alreadyHeldMsg`：分布式锁已被持有时(获取锁失败)的提示信息，默认提示信息"获取分布式锁失败，请稍后重试"。可通过SPEL表达式进行配置，不支持调用方法，可用如下方式获取分布式锁的key :  `#chamcDisLockKey`
- `realseAfterReturn`：是否在方法返回后立即释放分布式锁，默认立即释放。**仅在希望资源不可在一段时间内重复访问时设置为不立即释。不立即释放时，请设置过期时间。**
- `lockType`：分布式锁的模式(阻塞/非阻塞)，同时可配置非阻塞模式下尝试获取锁的超时时间，使用自定义注解`@BlockConfig`进行配置。**默认为非阻塞模式，仅尝试获取一次。**
- `@BlockConfig`：用于分布式锁模式配置的自定义注解。
	- `isBlocking`：是否阻塞模式，阻塞模式下等待时间配置生效。**默认为非阻塞模式**。
	- `waitTime`：等待时间，即在阻塞状态下，尝试获取锁的超时时间。**默认无超时时间，仅尝试获取一次，单位为毫秒**

##3 示例

###3.1 基础使用

【简单示例】

> - 如下示例，使用SPEL表达式可获取请求参数，对于分布式锁的key请选择能够标识资源的全限定字符串。[Spring4.3.x的SPEL表达式参考](https://docs.spring.io/spring-framework/docs/4.3.x/spring-framework-reference/html/expressions.html "Spring的SPEL表达式参考")
> - 示例同时展示了对锁过期时间和自定义提示信息的配置。

	@PostMapping("test")
	@DistributedLock(key = "#param.name + #param.value", leaseTime = 100000, 
			alreadyHeldMsg = "'锁已被持有，请稍后重试'")
	public void testA(@RequestBody PostLockParam param) {

	}

【方法幂等示例】

> 如下示例展示了对方法幂等功能的使用，增加了`realseAfterReturn = false`的配置，含义为方法返回后不自动释放分布式锁(抛错后会自动释放),利用失效时间(leaseTime)自动释放。用于需要某方法在一段时间内不允许重复操作同一资源的情况，**使用时建议配置失效时间**。

	@PostMapping("test")
	@DistributedLock(key = "#param.name + #param.value", leaseTime = 60000, 
			alreadyHeldMsg = "'锁已被持有，请稍后重试'", realseAfterReturn = false)
	public void testB(@RequestBody PostLockParam param) {

	}


【重入和参数示例】

> 如下示例展示了对大部分参数的配置：
> 
> - 对于key和alreadyHeldMsg，支持SPEL表达式。key支持对参数和方法的解析，其中方法必须为注入Spring的bean的公有方法。alreadyHeldMsg不支持方法调用，仅支持对参数解析和获取key，可使用`#chamcDisLockKey`获取分布式锁的key。
> - lockType的配置示例，如下使用自定义注解@BlockConfig进行配置，配置使用阻塞模式，获取等待时间为10000毫秒(10s)。
> - 示例演示了在key中对于方法的调用`@testController.userId()`，其中`@`表明`testController`是对bean的引用，此处使用的是TestController作为bean注入后的默认名。`userId()`方法模拟获取了用户账户。
> - 示例同时演示了分布式锁的重入，重入方法为`testReEnt`。由于对重入方法的调用发生在切面中，且重入要求为相同线程，请在使用重入功能时着重考虑。

	@PostMapping("test")
	@DistributedLock(key = "#param.name + ':' + #param.value + ':' + @testController.userId()",
			alreadyHeldMsg = "'锁已被持有,name=' + #param.name + ',key=' + #chamcDisLockKey", 
			leaseTime = 100000, lockType = @BlockConfig(isBlocking = true, waitTime = 10000))
	public void testC(@RequestBody PostLockParam param) {
		for(int i = 0;i < 10;i++) {
			SpringUtils.getBean(TestController.class).testReEnt(param);
		}
	}
	
	@DistributedLock(key = "#param.name + ':' + #param.value + ':' + @testController.userId()",
			alreadyHeldMsg = "'锁已被持有,name=' + #param.name + ',key=' + #chamcDisLockKey", 
			leaseTime = 100000)
	public void testReEnt(PostLockParam param) {
		System.out.println("111");
	}

	public String userId() {
		return "xxx@xxxxx.com.cn";
	}


###3.2 自定义处理

后台框架目前在在获取/释放锁失败时进行抛错处理，如需进行自定义处理请参考此段说明。

分布式锁的获取与释放功能使用`IDistributedLockService`接口完成，目前只支持通过Redis实现。

	public interface IDistributedLockService {
		// 阻塞获取分布式锁
		void lock(String key);

		// 阻塞获取，参数为锁过期时间(leaseTime)
		void lock(String key, long leaseTime);

		// 尝试获取锁，尝试时间为waitTime
		boolean tryLock(String key, long waitTime) throws DistributedLockException;

		// 尝试获取，参数为等待时间(waitTime)和过期时间(leaseTime)
		boolean tryLock(String key, long waitTime, long leaseTime) throws DistributedLockException;

		// 解锁方法
		void unlock(String key);
	}

可通过此接口实现获取/释放分布式锁过程中、抛错后的自定义处理，可参考后端框架在切面`DistributedLockAdvice`中的已有实现方式。如有任何问题请及时与我们联系。
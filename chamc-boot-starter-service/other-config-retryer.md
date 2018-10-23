# 重试机制

当ErrorDecoder中抛出`feign.RetryableException`时，会进行重试。可根据不同的接口使用不同的策略。

## 使用

实现`com.chamc.boot.service.config.feign.retryer.IFeignRetryerCustomizer`接口并将其注册到spring容器中（可注册多个）。其中包括以下两个方法。

	// 是否定制化重试策略
	boolean canCustomize(Class<?> targetType, Method method);
	// 重试策略参数
	void customize(FeignRetryerProperties properties);

`com.chamc.boot.service.config.feign.retryerFeignRetryerProperties`中包括三个属性：

- maxAttempts ：最大重试次数，默认5 （包括本次请求）
- period ：初始重试时间间隔，默认100(ms) （每次重试，时间间隔会递增）
- maxPeriod ：最大重试时间间隔，默认1000(ms)

## 示例

为TestFeignService这个feign配置重试策略。

	@Component
	public class TestFeignRetryer implements IFeignRetryerCustomizer {
	
		@Override
		public boolean canCustomize(Class<?> targetType, Method method) {
			return targetType.equals(TestFeignService.class);
		}
	
		@Override
		public void customize(FeignRetryerProperties properties) {
			properties.setMaxAttempts(2);
			properties.setPeriod(1000L);
			properties.setMaxPeriod(5000L);
		}
	
	}
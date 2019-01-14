# Contract个性化定制

当FeignClient注册时，feign通过feign.Contract决定FeignClient接口中哪些注解或者值生效。

## 使用说明

实现`com.chamc.boot.service.config.feign.contract.IFeignClientCustomizer`接口并将其注册到spring容器中（可注册多个）。其中包括以下两个方法。此方法在构建FeignClient的时候被调用，请求时不会被调用。

	// 是否需要个性化定制
	boolean canCustomize(Class<?> targetType, Method method);
	// 个性化定制操作
	void customize(RequestTemplate template);

## 示例

在所有FeignClient名为"xxx"的接口的请求中，加一个名为token的header。如下：

	@Component
	public class PmcFeignClientCustomizer implements IFeignClientCustomizer{
	
		@Override
		public boolean canCustomize(Class<?> targetType, Method method) {
			FeignClient feignClient = targetType.getAnnotation(FeignClient.class);
			if (Objects.isNull(feignClient)) {
				return false;
			}
			return "xxx".equals(feignClient.name());
		}
	
		@Override
		public void customize(RequestTemplate template) {
			template.header("token", "XXXXXXXXXXXXX");
		}
	
	}

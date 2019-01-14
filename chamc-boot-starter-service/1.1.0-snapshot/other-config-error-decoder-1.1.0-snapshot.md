# 异常处理

在feign请求发生异常时，应用系统能够针对不同的接口做个性化的处理。

## 使用

实现`com.chamc.boot.service.config.feign.codec.IFeignErrorDecoder`接口并将其注册到spring容器中（可注册多个）。其中包括以下两个方法。
	
	// 是否做异常处理
	boolean canErrorDecode(Class<?> targetType, Method method);
	// 处理Response返回一个异常
	Exception decode(Response response);

若返回`feign.RetryableException`异常，会重试，默认的重试机制为：最大尝试次数5次（包括本次请求），初始重试间隔100ms，每次递增，最大间隔1000ms。

## 示例

对TestFeignService的名为getUrl方法进行个性化异常处理，如下：

	@Component
	public class TestFeignErrorDecoder implements IFeignErrorDecoder{
	
		@Override
		public boolean canErrorDecode(Class<?> targetType, Method method) {
			return targetType.equals(TestFeignService.class) && method.getName().equals("getUrl");
		}
	
		@Override
		public Exception decode(Response response) {
			if (response.status() != 400) {			
				return new RetryableException("报错啦,亲!重试一下", new Date());
			}
			return new BussinessException(response.reason());
		}
	
	}
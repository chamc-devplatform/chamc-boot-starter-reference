# 请求拦截

应用系统可以对不同的接口做不同的拦截处理。与Contract的区别在于：

- Contract只在构建FeignClient时调用一次，而请求时不调用
- Interceptor在每次请求时都会调用

## 使用

实现`com.chamc.boot.service.config.feign.interceptor.IFeignInterceptor`接口并将其注册到spring容器中（可注册多个）。其中包括以下两个方法。

	// 是否进行拦截
	boolean canIntercept(Class<?> targetType, Method method);
	// 拦截处理
	void apply(RequestTemplate template);

## 示例

对TestFeignService中名为getUrl的方法进行拦截，并做相应的处理。

	@Component
	public class TestIFeignInterceptor implements IFeignInterceptor {
	
		@Override
		public boolean canIntercept(Class<?> targetType, Method method) {
			return TestFeignService.class.equals(targetType) && method.getName().equals("getUrl");
		}
	
		@Override
		public void apply(RequestTemplate template) {
			System.out.println("拦截了一下！！");
		}
	
	}
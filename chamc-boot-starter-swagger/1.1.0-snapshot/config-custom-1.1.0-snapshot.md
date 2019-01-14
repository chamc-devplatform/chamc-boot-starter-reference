# 个性化定制 #

## 背景 ##
swagger是根据spring容器里注册的Docket对象来生成在线文档的，Docket对象有很多关于在线文档生成方面的配置方法，基本配置有设置group，设置path等，我们可以实现DocketCustomizer来设置更多的自定义内容。

## 使用方法 ##

新建Customize类，实现DocketCustomizer，并重写其中customize(Docket docket)方法。

示例：在调用api进行服务请求时，需要在header中加入token才可以通过验证。直接使用swagger调用参数，会出现403错误。这里我们可以实现DocketCustomizer，在全局的请求中加入一个X-SERVICE-ID-TOKEN的参数，这样每次请求时，swagger会帮助我们把填入的token放入header中，验证通过后即可获得正确的返回值。代码示例及效果如下：

	public class TokenDocketCustomize implements DocketCustomizer {
		@Override
		public void customize(Docket docket) {
			ParameterBuilder tokenParamBuilder = new ParameterBuilder();
			Parameter tokenParam = tokenParamBuilder
					.name("X-SERVICE-ID-TOKEN")
					.modelRef(new ModelRef("string"))
					.parameterType("header")
					.required(true)
					.build();
			docket.globalOperationParameters(Arrays.asList(tokenParam));
		}
	}

![](https://i.imgur.com/AAE9wug.png)

# 其他配置

Spring Cloud的Feign支持的核心概念(之一)是命名客户端。每个Feign客户端都是组件集合的一部分，它们按需共同工作去链接远程服务器，该集合的名称由开发人员定义在`@FeignClient`注解中。Spring Cloud为每个使用`FeignClientConfiguration`的命名客户端按需创建ApplicationContext-组件集合。集合包括`feign.Decoder`，`feign.Encoder`和`feign.Contract`。

Spring默认为Feign提供了以下Bean：

- Decoder
- Encoder
- Logger
- Contract
- Feign.Builder
- Client

Spring并不默认提供以下bean，但是创建Feign客户端时，仍然会去application上下文中寻找这些类型的bean:

- Logger.Level
- Retryer
- ErrorDecoder
- Request.Options
- Collection<RequestInterceptor>
- SetterFactory

service组件对`Contract`、`Logger.Level`、`Retryer`、`ErrorDecoder`、`Collection<RequestInterceptor>`进行了扩展，使用户能够对其进行定制化处理。下面将分别介绍各个配置的用法。

【参考文档】
[http://cloud.spring.io/spring-cloud-static/Dalston.SR5/multi/multi_spring-cloud-feign.html](http://cloud.spring.io/spring-cloud-static/Dalston.SR5/multi/multi_spring-cloud-feign.html)
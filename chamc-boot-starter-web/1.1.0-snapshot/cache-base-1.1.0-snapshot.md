# 基本使用

## 说明

开发平台目前提供两种类型的缓存

- simple：内存式缓存，速度较快，不支持分布式，重启系统后数据消失
- redis：key-value存储系统，支持分布式，支持很多功能如排序、过期时间设置等，机器重启后热点数据不丢失，目前公司有redis缓存平台支持

## 缓存的使用配置

添加完`chamc-boot-starter-web`的maven依赖后，添加必要的配置信息就可以使用缓存了。如果是只是用simple或者redis缓存的话，按照如下所示添加对应的配置即可，cache-names为缓存名称，对于需要缓存的数据需要指定缓存名称，如后面示例代码所示。

1. simple

        #缓存类型为simple
        chamc.cache.type=simple
        #是否缓存值为null的缓存
        chamc.cache.cache-null-value=true
        #chamc的缓存名称配置
        chamc.cache.cache-names=cacheName1,cacheName2
        #spring的缓存名称配置
        spring.cache.cache-names=cacheName3,cacheName4

2. redis

        chamc.cache.type=redis
        chamc.cache.cache-null-value=true
        chamc.cache.cache-names=cacheName1,cacheName2
        spring.cache.cache-names=cacheName3,cacheName4
        #操作模板类型，有STRING和OBJECT两种，默认OBJECT。
        #OBJECT：将key和value序列化后存入redis，要求key和value都实现了Serializable；
        #STRING：key和value都是String时可以使用，效率比OBJECT高
        chamc.cache.template=object
        #默认过期时间，针对所有缓存，单位：ms，0表示永不过期，默认：30 * 60 * 1000L（30分钟）
        chamc.cache.default-expiration=1000
        #【redis】过期时间，针对单个缓存
        chamc.cache.expirations.cacheName1=1000

## 缓存的示例使用

如上配置示例所示，配置了cacheName1、cacheName2、cacheName3、cacheName4的cache，然后在service中的方法上添加注解`@Cacheable（cacheName）`即可。

    public class TestCacheService {
    
    	@org.springframework.cache.annotation.Cacheable("cacheName1")
    	public Dictionary testCache1() {
    		System.out.println("testing");
    		return new Dictionary("sex", "body", "男");
    	}
    	
    	@org.springframework.cache.annotation.Cacheable("cacheName2")
    	public Dictionary testCache2() {
    		System.out.println("testing");
    		return new Dictionary("class", "fly", "飞翔班");
    	}
    	
    }

## 关于cache的四个注解

包含了缓存策略、缓存刷新、缓存清除等

- @Cacheable
- @CachePut
- @CacheEvict
- @Caching

示例代码及说明如下：

    import java.util.Arrays;
    import java.util.List;
    
    import org.springframework.cache.annotation.CacheEvict;
    import org.springframework.cache.annotation.CachePut;
    import org.springframework.cache.annotation.Cacheable;
    import org.springframework.cache.annotation.Caching;
    
    import com.chamc.boot.web.support.dict.Dictionary;
    
    public class TestCacheService {
    
    	/**
    	 * cache发生在cacheName1上
    	 * 
    	 * @Cacheable可以标记在一个方法上，也可以标记在一个类上。当标记在一个方法上时表示该方法是支持缓存的，当标记在一个类上时则表示该类所有的方法都是支持缓存的。
    	 * 
    	 * spring在调用该方法后会将返回值保存起来（即会先判断缓存中有没有，有的话直接拿），下次利用同样的参数请求时直接从缓存中获取结果
    	 * 
    	 * 参数：(https://www.cnblogs.com/fashflying/p/6908028.html)
    	 * 	value：指定cache的名称，可以指定多个，如value = {"cacheName1", "cacheName2"}
    	 * 	key：指定Spring缓存方法的返回结果时对应的key，支持SpringEL表达式，有默认策略和自定义策略，自定义策略例如key = "#sex" 或key = "#p0"
    	 * 	condition：SpringEL表达式指定，当结果为true时缓存
    	 */
    	@Cacheable(value = "dicts", key = "#sex", condition = "#sex != null")
    	public Dictionary findByCache(String code) {
    		System.out.println("testing");
    		return new Dictionary(code, "body", "男");
    	}
    	
    	/**
    	 * 可以用在类上和方法上
    	 * 
    	 * 参数同@Cacheable的参数
    	 * 
    	 * 每次都会执行方法，并将结果存入指定的缓存中
    	 * 
    	 * @return
    	 */
    	@CachePut("dicts")
    	public List<Dictionary> findToFreshCache(String sex) {
    		return Arrays.asList(new Dictionary("sex", "body", "男"));
    	}
    	
    	/**
    	 * @CacheEvict是用来标注在需要清除缓存元素的方法或类上的。当标记在一个类上时表示其中所有的方法的执行都会触发缓存的清除操作
    	 * 
    	 * 参数
    	 * 	value
    	 * 	key：表示需要清除的是哪个key，如未指定则会使用默认策略生成的key
    	 * 	condition：表示清除操作发生的条件
    	 * 	allEntries：boolean类型，表示是否需要清除缓存中的所有元素。默认为false， 当值为true时会忽略指定的key，将该cache的所有元素都清空
    	 * 	beforeInvocation：是否在该方法调用前清除缓存，如果为false，则会先执行方法，然后方法如果抛异常不能正常完成则不会触发清除操作
    	 * 	
    	 */
    	@CacheEvict(value = "dicts", allEntries = true)
    	public void deleteFromCache(String sex) {
    		
    	}
    	
    	/**
    	 * @Caching可以让我们在一个方法或者一个类上用多个cache注解
    	 * 
    	 * cacheable：指定@Cacheable
    	 * put：指定@CachePut
    	 * evict：指定@CacheEvict。
    	 */
    	@Caching(cacheable = @Cacheable("dicts"), evict = {@CacheEvict("dicts")})
    	public void caches() {
    		
    	}
    }
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
# 高级使用

## 说明

目前开发平台支持多缓存，即配置不同的cache使用不同的缓存

## 配置示例

如下示例，默认缓存用了simple，另外一个缓存用了redis

要注意的是：

>type=simple的时候，其他属性（比如过期时间等）也能点出来，但是这些属性在simple的时候是不支持的，所以配置了也无效。  
>各个缓存的cacheName不能相同，比如如下示例中默认缓存中有cacheName1的缓存的话，otherCache就不能再有cacheName1的缓存了。

    chamc.cache.multi.enable=true

    chamc.cache.multi.def.type=simple
    chamc.cache.multi.def.cache-null-value=true
    chamc.cache.multi.def.cache-names=cacheName1
    
    #otherCache为另外一个缓存的别名，可以随意，符合java命名规范即可
    chamc.cache.multi.caches.otherCache.type=redis
    chamc.cache.multi.caches.otherCache.cache-null-value=true
    chamc.cache.multi.caches.otherCache.cache-names=cacheName2
    chamc.cache.multi.caches.otherCache.template=object
    chamc.cache.multi.caches.otherCache.default-expiration=1000
    chamc.cache.multi.caches.otherCache.expirations.cacheName1=1000

## 示例

如上配置示例所示，配置了cacheName1、cacheName2的cache，然后在service中的方法上添加注解`@Cacheable（cacheName）`即可。

下面两个方法，testCache1会查询simple的缓存，testCache2会查询名称为otherCache的缓存

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
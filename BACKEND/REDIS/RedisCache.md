## Redis Cache 
- Reference : https://mangkyu.tistory.com/179
  

### Spring의 캐시(Cache) 추상화

#### 설정
- Config Bean에 @EnableCaching 어노테이션 추가
```java
@Configuration 
@EnableCaching 
public class CustomCacheConfig {
    @Bean
    public CacheManger cacheManager(RedisTemplate redisTemplate){
        try{
            redisTemplate.hasKey("test-key");
            RedisCacheManger redisCacheManager = new RedisCacheManger(redisTemplate);
            // key - expeirationTime Mapper 
            Map expirationMap = new Map<String,String>();
            redisCacheManger.setExpires(expirationMap);
            return redisCacheManger;
        }catch (Exception e){
            log.debug("cache exception");
            return new NoOpCacheManger();

        }
    }
}
``` 
#### @Cacheable 
캐시에 데이터가 없을 경우에는 기존의 로직을 실행한 후에 캐시에 데이터를 추가하고, 캐시에 데이터가 있으면 캐시의 데이터를 반환

```java 
// key : getCustomDto:#externalId 
// value : dto 
@Cacheable(cacheNames = EXT_ID, key="#root.methodName.concat(':').concat(#externalId)")
public CustomDto getCustomDto(String externalId){
    CustomDto dto = new CustomDto();
    // API 호출 or Repository 호출 등 데이터 로딩
    // dto.set ~~~ 
    return dto;
}
```


#### @CacheEvict 
##### cache 제거 
- TTL 설정하여 일정한 주기로 캐시를 제거
- @CacheEvict 사용하여 값이 변할 때 캐시를 제거 후 갱신

###### 속성
- 캐시에 저장된 값을 모두 제거할 필요가 있다면 allEntries 속성을 true
``` java
@CacheEvict(cacheNames = STATICCACHES , allEntires = true)
public void clearBestSeller() {
    log.debug("remove cache all entries")
}
```

#### @CachePut
실행 결과를 캐시에 저장하지만, 조회 시에 저장된 캐시의 내용을 사용하지는 않고 항상 메소드의 로직을 실행

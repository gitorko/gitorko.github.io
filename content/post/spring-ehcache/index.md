---
title: 'Spring - EhCache'
description: 'Spring - EhCache'
summary: 'Spring Boot integration with EhCache 3'
date: '2024-06-23'
aliases: [/spring-ehcache/]
author: 'Arjun Surendra'
categories: [Caching]
tags: [spring, spring-boot, ehcache]
toc: true
---

Spring Boot 3 with EhCache 3

Github: [https://github.com/gitorko/project98](https://github.com/gitorko/project98)

## EhCache

EhCache is an open-source cache library. Ehcache version 3 provides an implementation of a JSR-107 cache manager. 
It supports cache in memory and disk, It supports eviction policies such as LRU, LFU, FIFO. Ehcache uses Last Recently Used (LRU) eviction strategy for memory & Last Frequently Used (LFU) as the eviction strategy for disk store.

**HashMap vs Cache**

Disadvantage of using hashmap over cache is that hashmap can cause memory overflow without eviction & doesn't support write to disk.

Ehcache will only evict elements when putting elements and your cache is above threshold. Otherwise, accessing those expired elements will result in them being expired (and removed from the Cache). There is no thread that collects and removes expired elements from the Cache in the background.

### Types of store

![](cache-store.png)

1. On-Heap Store - stores cache entries in Java heap memory
2. Off-Heap Store -  primary memory (RAM) to store cache entries, cache entries will be moved to the on-heap memory automatically before they can be used.
3. Disk Store - uses a hard disk to store cache entries. SSD type disk would perform better.
4. Clustered Store - stores cache entries on the remote server

Memory areas supported by Ehcache:

1. On-Heap Store: Uses the Java heap memory to store cache entries and shares the memory with the application. The cache is also scanned by the garbage collection. This memory is very fast, but also very limited.
2. Off-Heap Store: Uses the RAM to store cache entries. This memory is not subject to garbage collection. Still quite fast memory, but slower than the on-heap memory, because the cache entries have to be moved to the on-heap memory before they can be used.
3. Disk Store: Uses the hard disk to store cache entries. Much slower than RAM. It is recommended to use a dedicated SSD that is only used for caching.

**@Cacheable vs @CachePut** 

`@Cacheable` will skip running the method, whereas `@CachePut` will actually run the method and then put its results in the cache.

You can also use `CacheEventListener` to track events like CREATED, UPDATED, EXPIRED, REMOVED.

Ehcache uses Last Recently Used (LRU) as the default eviction strategy for the memory stores when the cache is full. 
If a disk store is used and this is full it uses Last Frequently Used (LFU) as the eviction strategy.

You can enable spring actuator and look at the cache metrics

The `@CacheConfig` annotation allows us to define certain cache configurations at the class level. This is useful if certain cache settings are common for all methods.


```bash
@CacheConfig(cacheNames = "customerCache")
```

### Types of caching

![](cache-strategy.png)

1. Read-Cache-aside - Application queries the cache. If the data is found, it returns the data directly. If not it fetches the data from the SoR, stores it into the cache, and then returns.
2. Read-Through - Application queries the cache, cache service queries the SoR if not present and updates the cache and returns.
3. Write-Around - Application writes to db and to the cache.
4. Write-Behind / Write-Back - Application writes to cache. Cache is pushed to SoR after some delay periodically.
5. Write-through - Application writes to cache, cache service immediately writes to SoR.

### Code


{{< ghcode "https://raw.githubusercontent.com/gitorko/project98/main/src/main/java/com/demo/project98/config/CacheConfig.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project98/main/src/main/java/com/demo/project98/listener/CountryCacheListener.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project98/main/src/main/java/com/demo/project98/service/CountryService.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project98/main/src/main/java/com/demo/project98/service/CustomerService.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project98/main/src/main/java/com/demo/project98/service/NumberService.java" >}}

Notice the SQL is printed each time a db call happens, if the data is cached no DB call is made.

### Postman

Import the postman collection to postman

[Postman Collection](https://raw.githubusercontent.com/gitorko/project98/main/postman/Project98.postman_collection.json)

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project98/main/README.md" >}}

## References

[https://www.ehcache.org/documentation/3.0](https://www.ehcache.org/documentation/3.0)

[https://docs.spring.io/spring-boot/docs/2.7.2/reference/htmlsingle/#io.caching](https://docs.spring.io/spring-boot/docs/2.7.2/reference/htmlsingle/#io.caching)

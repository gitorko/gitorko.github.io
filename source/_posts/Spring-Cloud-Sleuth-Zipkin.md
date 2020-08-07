---
title: Spring Cloud Sleuth & Zipkin
date: 2020-08-05 00:00:00
tags: zipkin, sleuth
---

Spring cloud sleuth helps you trace a request and zipkin server help you trace in a distributed environment.

Github: [https://github.com/gitorko/project72](https://github.com/gitorko/project72)

<!-- more -->

<!-- toc -->

## Spring Cloud Sleuth & Zipkin

How do you trace & debug a request in a single server? Now when it is deployed in pods and scaled how do you trace a request in a distributed environment?
Spring Cloud Sleuth help you trace a request by appending unique trace id in the log statements. You can the publish such traces to the zipkin server which lets you visualize a request across distributed environment.
You can then see the latency of each request in a distributed transaction.


Internally it has 4 modules –

Collector – Once any component sends the trace data arrives to Zipkin collector daemon, it is validated, stored, and indexed for lookups by the Zipkin collector.
Storage – This module store and index the lookup data in backend. Cassandra, ElasticSearch and MySQL are supported.
Search – This module provides a simple JSON API for finding and retrieving traces stored in backend. The primary consumer of this API is the Web UI.
Web UI – A very nice UI interface for viewing traces.

To run zipkin server use the docker command

```bash
docker run -d -p 9411:9411 openzipkin/zipkin
```

Once the server is up you should be able to login to the the zipkin UI

[http://localhost:9411/zipkin/](http://localhost:9411/zipkin/)


## SpringBoot Rest Service

Application.java

```java
package com.demo.project72;

import java.util.concurrent.Executor;
import java.util.concurrent.Executors;

import brave.Span;
import brave.Tracer;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.BeanFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.sleuth.instrument.async.LazyTraceExecutor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.Async;
import org.springframework.scheduling.annotation.AsyncConfigurerSupport;
import org.springframework.scheduling.annotation.EnableAsync;
import org.springframework.scheduling.annotation.EnableScheduling;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.scheduling.annotation.SchedulingConfigurer;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;
import org.springframework.scheduling.config.ScheduledTaskRegistrar;
import org.springframework.stereotype.Service;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}

@Service
@Slf4j
@EnableScheduling
class SchedulingService {

    @Autowired
    private SleuthService sleuthService;

    @Scheduled(fixedDelay = 30000)
    public void scheduledWork() throws InterruptedException {
        log.info("Start some work from the scheduled task");
        sleuthService.asyncMethod();
        log.info("End work from scheduled task");
    }
}

@RestController
@Slf4j
class SleuthController {

    @Autowired
    private SleuthService sleuthService;
    @Autowired
    private Executor executor;

    @GetMapping("/")
    public String helloSleuth() {
        log.info("Hello Sleuth");
        return "success";
    }

    @GetMapping("/same-span")
    public String helloSleuthSameSpan() throws InterruptedException {
        log.info("Same Span");
        sleuthService.doSomeWorkSameSpan();
        return "success";
    }

    @GetMapping("/new-span")
    public String helloSleuthNewSpan() throws InterruptedException {
        log.info("New Span");
        sleuthService.doSomeWorkNewSpan();
        return "success";
    }

    @GetMapping("/new-thread")
    public String helloSleuthNewThread() {
        log.info("New Thread");
        Runnable runnable = () -> {
            try {
                Thread.sleep(1000L);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            log.info("I'm inside the new thread - with a new span");
        };
        executor.execute(runnable);

        log.info("I'm done - with the original span");
        return "success";
    }

    @GetMapping("/async")
    public String helloSleuthAsync() throws InterruptedException {
        log.info("Before Async Method Call");
        sleuthService.asyncMethod();
        log.info("After Async Method Call");
        return "success";
    }
}

@Service
@Slf4j
@EnableAsync
class SleuthService {

    @Autowired
    private Tracer tracer;

    public void doSomeWorkSameSpan() throws InterruptedException {
        Thread.sleep(1000L);
        log.info("Doing some work");
    }

    public void doSomeWorkNewSpan() throws InterruptedException {
        log.info("I'm in the original span");

        Span newSpan = tracer.nextSpan().name("newSpan").start();
        try (Tracer.SpanInScope ws = tracer.withSpanInScope(newSpan.start())) {
            Thread.sleep(1000L);
            log.info("I'm in the new span doing some cool work that needs its own span");
        } finally {
            newSpan.finish();
        }

        log.info("I'm in the original span");
    }

    @Async
    public void asyncMethod() throws InterruptedException {
        log.info("Start Async Method");
        Thread.sleep(1000L);
        log.info("End Async Method");
    }
}

@Configuration
class ThreadConfig extends AsyncConfigurerSupport implements SchedulingConfigurer {

    @Autowired
    private BeanFactory beanFactory;

    @Bean
    public Executor executor() {
        ThreadPoolTaskExecutor threadPoolTaskExecutor = new ThreadPoolTaskExecutor();
        threadPoolTaskExecutor.setCorePoolSize(1);
        threadPoolTaskExecutor.setMaxPoolSize(1);
        threadPoolTaskExecutor.initialize();

        return new LazyTraceExecutor(beanFactory, threadPoolTaskExecutor);
    }

    @Override
    public Executor getAsyncExecutor() {
        ThreadPoolTaskExecutor threadPoolTaskExecutor = new ThreadPoolTaskExecutor();
        threadPoolTaskExecutor.setCorePoolSize(1);
        threadPoolTaskExecutor.setMaxPoolSize(1);
        threadPoolTaskExecutor.initialize();

        return new LazyTraceExecutor(beanFactory, threadPoolTaskExecutor);
    }

    @Override
    public void configureTasks(ScheduledTaskRegistrar scheduledTaskRegistrar) {
        scheduledTaskRegistrar.setScheduler(schedulingExecutor());
    }

    @Bean(destroyMethod = "shutdown")
    public Executor schedulingExecutor() {
        return Executors.newScheduledThreadPool(1);
    }
}
```

application.yaml

```yaml
spring:
  application:
    name: project72
  sleuth:
    enabled: true
    sampler:
      probability: 1.0
  zipkin:
    base-url: http://localhost:9411/
    enabled: true
    sender:
      type: web
    service:
      name: my-service
```


build.gradle

```groovy
plugins {
    id 'org.springframework.boot' version '2.3.2.RELEASE'
    id 'io.spring.dependency-management' version '1.0.9.RELEASE'
    id 'java'
}

group = 'com.demo'
version = '1.0.0'
sourceCompatibility = '11'

dependencyManagement {
    imports {
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:Hoxton.SR7"
    }
}

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    compile 'org.springframework.cloud:spring-cloud-starter-sleuth'
    compile 'org.springframework.cloud:spring-cloud-starter-zipkin'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
}
```

Run the project

```bash
./gradlew bootRun
```

Hit the rest endpoint couple of times.
[http://localhost:8080/](http://localhost:8080/)

You will now see the log messages appended with a unique tracer, the last flag with 'true' indicates that the data is sent to zipkin server

```
2020-08-05 20:52:21.948  INFO [my-service,c61a37e8c6dd78e8,42102f3f9d4e1910,true] 16218 --- [lTaskExecutor-1] com.demo.project72.SleuthService : Start Async Method
2020-08-05 20:52:22.951  INFO [my-service,c61a37e8c6dd78e8,42102f3f9d4e1910,true] 16218 --- [lTaskExecutor-1] com.demo.project72.SleuthService : End Async Method
```
You can now view the trace in zipkin UI

{% asset_img image01.png %}

## References
Spring Cloud Sleuth : [https://cloud.spring.io/spring-cloud-sleuth/](https://cloud.spring.io/spring-cloud-sleuth/)
Zipkin : [https://zipkin.io/](https://zipkin.io/)

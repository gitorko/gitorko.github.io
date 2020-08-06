---
title: Spring Application Events
date: 2020-08-06 00:00:00
tags:
---

Spring application events provides functionality for async tasks to communicate with each other.

Github: [https://github.com/gitorko/project73](https://github.com/gitorko/project73)

<!-- more -->

<!-- toc -->

## Spring Application Events

Lets say you have 2 async functions that needs co-ordinate. When async1 is complete you need async2 to pick up the processed data and process it. You could use an external queue like rabbitmq, or you could write a producer consumer. Spring events lets you handle such a task easily. Do note that this will be in-memory so if the server restarts all events will be lost.

## Code

You can now create a gradle project using using [start.spring.io](http://start.spring.io/)

Application.java

```java
package com.demo.project73;

import java.util.Date;
import java.util.concurrent.TimeUnit;
import java.util.stream.IntStream;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.context.event.ApplicationReadyEvent;
import org.springframework.context.ApplicationEventPublisher;
import org.springframework.context.event.EventListener;
import org.springframework.scheduling.annotation.Async;
import org.springframework.scheduling.annotation.EnableAsync;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Service;

@SpringBootApplication
public class Application implements ApplicationRunner {

    @Autowired
    MyEventListener myEventListener;

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Override
    public void run(ApplicationArguments arg0) throws Exception {
        myEventListener.doSomeAsyncTask1();
    }
}

@AllArgsConstructor
@Data
class MyEvent{
    String name;
}

@Slf4j
@EnableAsync
@Service
class MyEventListener {

    @Autowired
    ApplicationEventPublisher applicationEventPublisher;

    @EventListener(ApplicationReadyEvent.class)
    public void onStart() {
        log.info("Triggered when application ready!");
    }

    @Async
    @EventListener
    public void doSomeAsyncTask2(MyEvent myEvent) {
        log.info("Received event {}", myEvent);
        sleep(10);
        log.info("Processed event {}", myEvent);
    }

    @Async
    public void doSomeAsyncTask1() {
        log.info("Running task!");
        IntStream.range(0,5).forEach(i -> {
            sleep(1);
            log.info("Trigger event: {}", i);
            applicationEventPublisher.publishEvent(new MyEvent("EventId: " + i));
        });
    }

    @SneakyThrows
    private void sleep(int seconds) {
        TimeUnit.SECONDS.sleep(seconds);
    }
}
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

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
}
```

Run the project

```bash
./gradlew bootRun
```

You will now see that after doSomeAsyncTask1 is run it publishes a event and doSomeAsyncTask2 picks up the event and both run asynchronously.
```
Triggered when application ready!
Running task!
Trigger event: 0
Received event MyEvent(name=EventId: 0)
Trigger event: 1
Received event MyEvent(name=EventId: 1)
Trigger event: 2
Received event MyEvent(name=EventId: 2)
Trigger event: 3
Received event MyEvent(name=EventId: 3)
Trigger event: 4
Received event MyEvent(name=EventId: 4)
Processed event MyEvent(name=EventId: 0)
Processed event MyEvent(name=EventId: 1)
Processed event MyEvent(name=EventId: 2)
Processed event MyEvent(name=EventId: 3)
Processed event MyEvent(name=EventId: 4)
```

## References
Spring Application Events : [https://spring.io/blog/2015/02/11/better-application-events-in-spring-framework-4-2](https://spring.io/blog/2015/02/11/better-application-events-in-spring-framework-4-2)

---
title: Spring AMQP RPC
date: 2020-03-15 00:00:00
tags: 
- spring amqp
- rabbitmq
- rpc
categories:
- [Messaging]
- [Spring]
---

RabbitMQ is a message broker that implements Advanced Message Queing Protocol(AMQP).
Remote procedure call (RPC) is a way to invoking a function on another computer and waiting for the result. The call is syncronous and blocking in nature, so the client will wait for the response.

Github: [https://github.com/gitorko/project78](https://github.com/gitorko/project78)

<!-- more -->

<!-- toc -->

## RabbitMQ RPC

Run the docker command to start a rabbitmq instance

```bash
docker run -d --hostname my-rabbit -p 8080:15672 -p 5672:5672 rabbitmq:3-management
```

You can login to the rabbitmq console with username:guest password: guest

[http://localhost:8080](http://localhost:8080)

## Code

RPCDemoServer.java

```java
package com.demo.project78.rpc.server;

import java.util.Scanner;
import java.util.concurrent.TimeUnit;

import lombok.SneakyThrows;
import org.springframework.amqp.core.Binding;
import org.springframework.amqp.core.BindingBuilder;
import org.springframework.amqp.core.DirectExchange;
import org.springframework.amqp.core.Queue;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.stereotype.Component;

@Configuration
@EnableAutoConfiguration
@ComponentScan(basePackages = "com.demo.project78.rpc.server")
public class RPCDemoServer implements CommandLineRunner {
    public static final String directExchangeName = "rpc-exchange";
    public static final String queueName = "rpc-queue";
    public static final String routingKey = "rpc";

    @Bean
    Queue queue() {
        return new Queue(queueName, false);
    }

    @Bean
    DirectExchange exchange() {
        return new DirectExchange(directExchangeName);
    }

    @Bean
    Binding binding(Queue queue, DirectExchange exchange) {
        return BindingBuilder.bind(queue).to(exchange).with(routingKey);
    }

    public static void main(String[] args) throws InterruptedException {
        SpringApplication.run(RPCDemoServer.class, args).close();
    }

    @Override
    public void run(String... args) throws Exception {
        System.out.println("Waiting...");
        new Scanner(System.in).nextLine();

    }
}

@Component
class RpcServer {

    @SneakyThrows
    @RabbitListener(queues = RPCDemoServer.queueName)
    public String LongRunningTask(String name) {
        System.out.println("Received: " + name);
        //Long running processing
        TimeUnit.SECONDS.sleep(15);
        return "Hello world, " + name;
    }

}
```

RPCDemoClient.java

```java
package com.demo.project78.rpc.client;

import org.springframework.amqp.core.DirectExchange;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.stereotype.Component;

@Configuration
@EnableAutoConfiguration
@ComponentScan(basePackages = "com.demo.project78.rpc.client")
public class RPCDemoClient implements CommandLineRunner {

    public static final String directExchangeName = "rpc-exchange";
    public static final String routingKey = "rpc";

    @Autowired
    RpcClient rpcClient;

    @Bean
    DirectExchange exchange() {
        return new DirectExchange(directExchangeName);
    }

    public static void main(String[] args) throws InterruptedException {
        SpringApplication.run(RPCDemoClient.class, args).close();
    }

    @Override
    public void run(String... args) throws Exception {
        rpcClient.send("John");
    }

}

@Component
class RpcClient {

    @Autowired
    private RabbitTemplate template;

    @Autowired
    private DirectExchange exchange;

    public void send(String name) {
        System.out.println("Sending name: " + name);
        template.setReplyTimeout(60000);
        String response = (String) template.convertSendAndReceive(exchange.getName(), RPCDemoClient.routingKey, name);
        System.out.println("Got '" + response + "'");
    }
}
```

application.properties

```yaml
spring:
  main:
    web-application-type: none
```


build.gradle

```groovy
plugins {
    id 'org.springframework.boot' version '2.3.3.RELEASE'
    id 'io.spring.dependency-management' version '1.0.10.RELEASE'
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
    implementation 'org.springframework.boot:spring-boot-starter-amqp'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
}
```

Run the server code first and then run the client. The client waits for 60 seconds before timeout.

## References
[https://spring.io/projects/spring-amqp](https://spring.io/projects/spring-amqp)
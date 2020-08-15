---
title: Spring AMQP
date: 2020-03-14 00:00:00
tags: 
- spring amqp
- rabbitmq
categories:
- [Messaging]
- [Spring]
---

RabbitMQ is a message broker that implements Advanced Message Queing Protocol(AMQP).

Github: [https://github.com/gitorko/project78](https://github.com/gitorko/project78)

<!-- more -->

<!-- toc -->

## RabbitMQ

Exchanges are like post offices or mailboxes and clients publish a message to an AMQP exchange. 
There are four built-in exchange types
    1. Direct Exchange – Routes messages to a queue by matching a complete routing key
    2. Fanout Exchange – Routes messages to all the queues bound to it
    3. Topic Exchange – Routes messages to multiple queues by matching a routing key to a pattern
    4. Headers Exchange – Routes messages based on message headers
Queues are bound to an exchange using a routing key. Messages are sent to an exchange with a routing key.

Run the docker command to start a rabbitmq instance

```bash
docker run -d --hostname my-rabbit -p 8080:15672 -p 5672:5672 rabbitmq:3-management
```

You can login to the rabbitmq console with username:guest password: guest

[http://localhost:8080](http://localhost:8080)

{% asset_img image01.png %}

## Code

The producer will publish the message to the direct exchange with routing key and the consumer consumes this message asynchronously.

TopicExchangeDemo.java

```java
package com.demo.project78;

import java.util.concurrent.CountDownLatch;
import java.util.concurrent.TimeUnit;

import lombok.Data;
import lombok.RequiredArgsConstructor;
import org.springframework.amqp.core.Binding;
import org.springframework.amqp.core.BindingBuilder;
import org.springframework.amqp.core.Queue;
import org.springframework.amqp.core.TopicExchange;
import org.springframework.amqp.rabbit.connection.ConnectionFactory;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.amqp.rabbit.listener.SimpleMessageListenerContainer;
import org.springframework.amqp.rabbit.listener.adapter.MessageListenerAdapter;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.stereotype.Component;

@SpringBootApplication
public class TopicExchangeDemo {

    static final String topicExchangeName = "exchange1";

    static final String queueName = "queue1";
    @Bean
    Queue queue() {
        return new Queue(queueName, false);
    }

    @Bean
    TopicExchange exchange() {
        return new TopicExchange(topicExchangeName);
    }

    @Bean
    Binding binding(Queue queue, TopicExchange exchange) {
        return BindingBuilder.bind(queue).to(exchange).with("foo.bar.#");
    }

    @Bean
    SimpleMessageListenerContainer container(ConnectionFactory connectionFactory, MessageListenerAdapter listenerAdapter) {
        SimpleMessageListenerContainer container = new SimpleMessageListenerContainer();
        container.setConnectionFactory(connectionFactory);
        container.setQueueNames(queueName);
        container.setMessageListener(listenerAdapter);
        return container;
    }

    @Bean
    MessageListenerAdapter listenerAdapter(Receiver receiver) {
        return new MessageListenerAdapter(receiver, "receiveMessage");
    }

    public static void main(String[] args) throws InterruptedException {
        SpringApplication.run(TopicExchangeDemo.class, args).close();
    }
}

@Component
@Data
class Receiver {

    private CountDownLatch latch = new CountDownLatch(1);

    public void receiveMessage(String message) {
        System.out.println("Received <" + message + ">");
        latch.countDown();
    }

}

@Component
@RequiredArgsConstructor
class Runner implements CommandLineRunner {

    private final RabbitTemplate rabbitTemplate;
    private final Receiver receiver;

    @Override
    public void run(String... args) throws Exception {
        System.out.println("Sending message...");
        rabbitTemplate.convertAndSend(TopicExchangeDemo.topicExchangeName, "foo.bar.baz", "Hello from RabbitMQ!");
        receiver.getLatch().await(10000, TimeUnit.MILLISECONDS);
    }

}
```

You can use the simple queue as well if you dont want to use exchanges

QueueDemo.java

```java
package com.demo.project78;

import org.springframework.amqp.core.Queue;
import org.springframework.amqp.rabbit.annotation.RabbitHandler;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.stereotype.Component;

@SpringBootApplication
public class QueueDemo implements CommandLineRunner {

    @Autowired
    MessageSender messageSender;

    public static void main(String[] args) throws InterruptedException {
        SpringApplication.run(QueueDemo.class, args).close();
    }

    @Override
    public void run(String... args) throws Exception {
        messageSender.send();
    }

    @Bean
    public Queue hello() {
        return new Queue("queue2", false);
    }

}

@Component
class MessageSender {

    @Autowired
    private RabbitTemplate template;

    @Autowired
    private Queue queue;

    public void send() {
        String message = "Hello World!";
        template.convertAndSend(queue.getName(), message);
        System.out.println("Sent '" + message + "'");
    }
}

@RabbitListener(queues = "queue2")
class MessageReceiver {

    @RabbitHandler
    public void receive(String in) {
        System.out.println("Received '" + in + "'");
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

## References
[https://spring.io/projects/spring-amqp](https://spring.io/projects/spring-amqp)

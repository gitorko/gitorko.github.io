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

{% ghcode https://github.com/gitorko/project78/blob/master/src/main/java/com/demo/project78/TopicExchangeDemo.java %}

You can use the simple queue as well if you dont want to use exchanges

{% ghcode https://github.com/gitorko/project78/blob/master/src/main/java/com/demo/project78/QueueDemo.java %}

## References
[https://spring.io/projects/spring-amqp](https://spring.io/projects/spring-amqp)

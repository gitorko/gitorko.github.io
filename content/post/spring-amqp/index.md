---
title: 'Spring - RabbitMQ'
description: 'Spring - RabbitMQ'
summary: 'Spring Boot with RabbitMQ message broker that implements Advanced Message Queuing Protocol(AMQP)'
date: '2020-03-15'
aliases: [/spring-amqp/]
author: 'Arjun Surendra'
categories: [Messaging, RabbitMQ, Spring]
tags: [spring, spring-boot, rabbitmq, amqp, rpc]
toc: true
---

Spring with RabbitMQ message broker that implements Advanced Message Queuing Protocol(AMQP)

Github: [https://github.com/gitorko/project78](https://github.com/gitorko/project78)

## RabbitMQ

Exchanges are like post offices or mailboxes and clients publish a message to an AMQP exchange. 
There are four built-in exchange types

1. Direct Exchange – Routes messages to a queue by matching a complete routing key
2. Fanout Exchange – Routes messages to all the queues bound to it
3. Topic Exchange – Routes messages to multiple queues by matching a routing key to a pattern
4. Headers Exchange – Routes messages based on message headers

Queues are bound to an exchange using a routing key. Messages are sent to an exchange with a routing key.
AMQP (Advanced Message Queuing Protocol) is an open standard wire specification for asynchronous message communication, AMQP provides platform-neutral binary protocol standard, hence it can run on different environments & programming languages unlike JMS.

Remote procedure call (RPC) is a way to invoking a function on another computer and waiting for the result. The call is synchronous and blocking in nature, so the client will wait for the response.

![](img01.png)

## Code

Queue to send and receive messages

{{< ghcode "https://raw.githubusercontent.com/gitorko/project78/main/src/main/java/com/demo/project78/queue/QueueConfig.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project78/main/src/main/java/com/demo/project78/queue/QueueSender.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project78/main/src/main/java/com/demo/project78/queue/QueueReceiver.java" >}}

Direct exchange with routing key

{{< ghcode "https://raw.githubusercontent.com/gitorko/project78/main/src/main/java/com/demo/project78/exchange/ExchangeConfig.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project78/main/src/main/java/com/demo/project78/exchange/ExchangeSender.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project78/main/src/main/java/com/demo/project78/exchange/ExchangeReceiver.java" >}}


Fanout exchange

{{< ghcode "https://raw.githubusercontent.com/gitorko/project78/main/src/main/java/com/demo/project78/fanout/FanoutExchangeConfig.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project78/main/src/main/java/com/demo/project78/fanout/FanoutSender.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project78/main/src/main/java/com/demo/project78/fanout/FanoutReceiver.java" >}}

Topic exchange

{{< ghcode "https://raw.githubusercontent.com/gitorko/project78/main/src/main/java/com/demo/project78/topic/TopicExchangeConfig.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project78/main/src/main/java/com/demo/project78/topic/TopicSender.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project78/main/src/main/java/com/demo/project78/topic/TopicReceiver.java" >}}

Error handling & Exchange creation

{{< ghcode "https://raw.githubusercontent.com/gitorko/project78/main/src/main/java/com/demo/project78/config/AmqpConfig.java" >}}

RPC

{{< ghcode "https://raw.githubusercontent.com/gitorko/project78/main/src/main/java/com/demo/project78/rpc/config/RpcConfig.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project78/main/src/main/java/com/demo/project78/rpc/client/RpcClient.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project78/main/src/main/java/com/demo/project78/rpc/server/RpcServer.java" >}}

## Setup

{{< markcode "https://raw.githubusercontent.com/gitorko/project78/main/README.md" >}}

## References

[https://spring.io/projects/spring-amqp](https://spring.io/projects/spring-amqp)

[https://www.rabbitmq.com/tutorials/tutorial-six-spring-amqp.html](https://www.rabbitmq.com/tutorials/tutorial-six-spring-amqp.html)

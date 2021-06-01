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

RabbitMQ is a message broker that implements Advanced Message Queue Protocol(AMQP).
Remote procedure call (RPC) is a way to invoking a function on another computer and waiting for the result. The call is synchronous and blocking in nature, so the client will wait for the response.

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

{% ghcode https://github.com/gitorko/project78/blob/master/src/main/java/com/demo/project78/rpc/server/RPCDemoServer.java %}

{% ghcode https://github.com/gitorko/project78/blob/master/src/main/java/com/demo/project78/rpc/client/RPCDemoClient.java %}

Run the server code first and then run the client. The client waits for 60 seconds before timeout.

## References
[https://spring.io/projects/spring-amqp](https://spring.io/projects/spring-amqp)

---
title: Spring Cloud Sleuth & Zipkin
date: 2020-08-05 00:00:00
tags: 
- zipkin
- sleuth
categories:
- [Zipkin]
- [Sleuth]
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

{% ghcode https://github.com/gitorko/project72/blob/master/src/main/java/com/demo/project72/Application.java %}

{% ghcode https://github.com/gitorko/project72/blob/master/src/main/resources/application.yaml %}

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

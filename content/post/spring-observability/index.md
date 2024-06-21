---
title: 'Spring Boot - Observability'
description: 'Spring Boot - Observability'
summary: 'Spring Boot Observability'
date: '2024-06-09'
aliases: [/spring-boot-observability/, /spring-observability/]
author: 'Arjun Surendra'
categories: [MicroMeter, Grafana, Prometheus, Actuator, Observability, Tracing]
tags: [spring, spring-boot, prometheus, grafana, jmx, micrometer, observability, tracing, spring-security]
toc: true
---

Spring Boot Observability

Github: [https://github.com/gitorko/project71](https://github.com/gitorko/project71)

## Monitoring & Observability

- **Monitoring*** - ensures the system is healthy. With `spring-boot-starter-actuator` you can monitor CPU usage, memory usage, request rates, and error rates.
- **Observability** - helps you understand issues and derive insights. Micrometer Observability API  

Observability is the ability to observe the internal state of a running system from the outside. Observability has 3 pillars

1. **Logging** - Logging Correlation IDs - Correlation IDs provide a helpful way to link lines in your log files to spans/traces.
2. **Metrics** - Custom metrics to monitor time taken, count invocations etc.
3. **Distributed Tracing** - Micrometer Tracing library is a facade for popular tracer libraries. eg: OpenTelemetry, OpenZipkin Brave

Various tools that help in observability

1. **Prometheus** — An open-source systems monitoring and alerting tool. Prometheus scrapes/collects metrics from an endpoint at regular intervals. Stores the data in a time series database.
2. **Grafana** — A visualization tool, can pull data from multiple sources (Prometheus) and shows them in graphs.
3. **Zipkin** — a distributed tracing system. It helps gather timing data needed to troubleshoot latency problems in service architectures. Features include both the collection and lookup of this data.

### Logging

Micrometer tracing adds spans/traces to all logs.

### Metrics

A `Meter` consists of a name and tags, There are 4 main types of meters.

1. Timers - Time taken to run something.
2. Counter - Number of time something was run.
3. Gauge - Report data when observed. Gauges can be useful when monitoring stats of cache, collections
4. Distribution summary - Distribution of events.
5. Binders - Built-in binders to monitor the JVM, caches, ExecutorService, and logging services

### Distributed Tracing

Spring Boot samples only 10% of requests to prevent overwhelming the trace backend. Change probability to 1.0 so that every request is sent to the trace backend.

```yaml
management:
  tracing:
    sampling:
      probability: 1.0
```

### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project71/main/src/main/java/com/demo/project71/service/GreetService.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project71/main/src/main/java/com/demo/project71/config/RegistryConfig.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project71/main/src/main/java/com/demo/project71/config/ThreadConfig.java" >}}

### Postman

![](img11.png)

Import the postman collection to postman

[Postman Collection](https://raw.githubusercontent.com/gitorko/project71/main/postman/Project71.postman_collection.json)

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project71/main/README.md" >}}

Open zipkin dashboard

[http://localhost:9411/zipkin/](http://localhost:9411/zipkin/)

![](img01.png)

![](img02.png)

Open prometheus dashboard

[http://localhost:9090](http://localhost:9090)

![](img03.png)

![](img04.png)

Open grafana dashboard

[http://localhost:3000](http://localhost:3000)

```
user: admin
password: admin
```

Add the prometheus data source, make sure it's the ip address of your system, don't add localhost

http://IP-ADDRESS:9090

![](img05.png)

There are existing grafana dashboards that can be imported. Import a dashboard, Download the json file or copy the ID of the dashboard for micrometer dashboard.

https://grafana.com/dashboards/4701

![](img06.png)

![](img07.png)

![](img08.png)

![](img09.png)

Create a custom dashboard, Add a new panel, add 'hello_api_count_total' metric in the query, save the dashboard.

![](img10.png)

## References

[https://micrometer.io/docs](https://micrometer.io/docs)

[https://prometheus.io/](https://prometheus.io/)

[https://grafana.com/](https://grafana.com/)

[https://grafana.com/grafana/dashboards/4701](https://grafana.com/grafana/dashboards/4701)

[https://grafana.com/grafana/dashboards/](https://grafana.com/grafana/dashboards/)

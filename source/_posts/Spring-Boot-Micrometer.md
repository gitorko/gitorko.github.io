---
title: Spring Boot Micrometer
date: 2019-04-08 00:00:00
tags: prometheus, jmx, micrometer, spring boot, grafana
---

A Spring boot application integration with micrometer. Micrometer provides vendor neutral application metrics facade that can integrate with various monitoring systems like Prometheus, Wavefront, Atlas, Datadog, Graphite, Ganglia, Influx, JMX etc. We will look at integrations with prometheus and grafana in this example.

Github: [https://github.com/gitorko/project68](https://github.com/gitorko/project68)

<!-- more -->

<!-- toc -->

## Micrometer

Traditional systems which monitored JMX attributes could only do so at a particular instance of time. With the arrival of time series database we can now use that data and visualize it over a period in time. Writing the integration to various monitoring systems is time consuming, hence micrometer and spring simplyfy it. Underlying metrics are exposed by Spring Boot Actuator and then Micrometer provides a facade that can be used to either push or pull metrics to monitoring systems.

Every meter has a name (hierarchical) and tag. There are 4 main types of meters.
Timers  - Time taken to run something.
Counter - Number of time something was run.
Guages - Report data when observed. Gauges can be useful when monitoring stats of cache, collections
Distribution summary - Distribution of events.

MeterRegistryCustomizer, you can customize the whole set of registries at once or individual implementation.

## Prometheus

To start the prometheus docker instance run the command.

```bash
docker run --name pormetheus -d -p 9090:9090 -v c:\\project68\\prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
```

prometheus.yml

Ensure you change the target to an acutall IP address and not use localhost when using docker container.

```yaml
global:
  scrape_interval: 10s
  scrape_timeout: 5s
  evaluation_interval: 10s
alerting:
  alertmanagers:
    - static_configs:
        - targets: []
      scheme: http
      timeout: 10s
scrape_configs:
  - job_name: myapp
    scrape_interval: 10s
    scrape_timeout: 5s
    metrics_path: /actuator/prometheus
    scheme: http
    static_configs:
      - targets:
          - 10.112.68.227:8080
```

Prometheus [http://localhost:9090](http://localhost:9090)

## Spring boot

You can now create a gradle project using using [start.spring.io](http://start.spring.io/)

Application.java

```java
package com.demo.project68;

import io.micrometer.core.annotation.Timed;
import io.micrometer.core.instrument.Counter;
import io.micrometer.core.instrument.MeterRegistry;
import io.micrometer.core.instrument.Metrics;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.actuate.autoconfigure.metrics.MeterRegistryCustomizer;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.PostConstruct;
import java.util.Random;
import java.util.concurrent.TimeUnit;

@SpringBootApplication
@Slf4j
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}

@RestController
@RequestMapping("/api")
@Slf4j
class AppController {

    @Timed("hello.api.time")
    @GetMapping("/hello")
    public String sayHello() throws InterruptedException {
        RegistryConfig.helloApiCounter.increment();
        int sleepTime = new Random().nextInt(10);
        log.info("Sleeping for seconds: {}", sleepTime);
        TimeUnit.SECONDS.sleep(sleepTime);
        return "Hello, Sleep for " + sleepTime + " Seconds!";
    }
}

@Configuration
@EnableAspectJAutoProxy
class RegistryConfig {

    public static Counter helloApiCounter;

    @Bean
    MeterRegistryCustomizer<MeterRegistry> configurer(@Value("${spring.application.name}") String applicationName) {
        return registry -> registry.config().commonTags("application", applicationName);
    }

    @PostConstruct
    public void postInit() {
        helloApiCounter = Metrics.counter("hello.api.count", "type", "order");
    }
}

```

application.yaml

```yaml
server:
  port: 8080
management:
  metrics:
    export:
      prometheus:
        enabled: true
  endpoints:
    web:
      exposure:
        include: "*"
  endpoint:
    metrics:
      enabled: true
    prometheus:
      enabled: true
    metrics.enabled: true
spring:
  application:
    name: myapp
```


build.gradle

```groovy
plugins {
    id 'org.springframework.boot' version '2.1.4.RELEASE'
    id 'java'
}

apply plugin: 'io.spring.dependency-management'

group = 'com.demo'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '1.8'

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
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
    implementation 'org.springframework.boot:spring-boot-starter-aop'
    compile 'io.micrometer:micrometer-core:1.1.4'
    compile 'io.micrometer:micrometer-registry-jmx:1.1.4'
    compile 'io.micrometer:micrometer-registry-prometheus:1.1.4'
    compileOnly 'org.projectlombok:lombok'
    runtimeOnly 'org.springframework.boot:spring-boot-devtools'
    annotationProcessor 'org.projectlombok:lombok'
}

```

Run the project

```bash
./gradlew bootRun
```

Hit the rest endpoint couple of times.
[http://localhost:8080/api/hello](http://localhost:8080/api/hello)

Ensure that your custom metrics are populated in 
[http://localhost:8080/actuator/prometheus](http://localhost:8080/actuator/prometheus)

You should see metrics similar to 

```json
hello_api_count_total{application="myapp",type="order",} 27.0
hello_api_time_seconds_count{application="myapp",exception="None",method="GET",outcome="SUCCESS",status="200",uri="/api/hello",} 27.0
hello_api_time_seconds_sum{application="myapp",exception="None",method="GET",outcome="SUCCESS",status="200",uri="/api/hello",} 102.162818601
hello_api_time_seconds_max{application="myapp",exception="None",method="GET",outcome="SUCCESS",status="200",uri="/api/hello",} 0.0
```

Prometheus should now start pulling data from the spring application. Click on status -> targets on prometheus dashboard to confirm that endpoint is up.

{% asset_img image01.JPG %}

Query the metric hello_api_count_total and view as graph

{% asset_img image02.JPG %}

## Grafana

The dashboard in promethues are minimal, to add more complex dashboard and visualization you can look at Grafana. To start the grafana docker instance run the command.

```bash
docker run --name grafana -d -p 3000:3000 grafana/grafana
```
[http://localhost:3000/](http://localhost:3000/)

Login as admin/admin

Add the prometheus data source

{% asset_img image03.JPG %}

Import a dashboard, Downlaod the json file for micrometer dashboard and import it in grafana and open the dashboard.

[https://grafana.com/dashboards/4701](https://grafana.com/dashboards/4701)

{% asset_img image04.JPG %}

{% asset_img image05.JPG %}

Create a dashboard on the 'hello_api_count_total' metric

{% asset_img image06.JPG %}

## References
Micrometer Reference Document : [https://micrometer.io/docs](https://micrometer.io/docs)
Prometheus : [https://prometheus.io/](https://prometheus.io/)

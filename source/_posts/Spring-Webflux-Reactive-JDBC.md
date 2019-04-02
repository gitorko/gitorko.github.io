---
title: Spring Webflux & Reactive JDBC
date: 2019-04-02 00:00:00
tags: spring-webflux, jdbc,reactive support
---

We will learn how to integrate spring webflux with jdbc to allow non-blocking calls to database. We will be using postgres db but any db can be used.

<!-- more -->

<!-- toc -->

## Spring Webflux

Spring webflux provided non-blocking support for rest api. However with R2dbc still under experimental support cant be used for production applications. Hence the following approach provides a way to make non-blocking calls to JDBC.

You can create an empty postgres database with the following commands

```bash
psql -h localhost -U postgres postgres
    postgres=# CREATE USER demodb WITH PASSWORD 'demopwd';
    postgres=# CREATE DATABASE demodb WITH OWNER demouser ENCODING UTF8 TEMPLATE template0;
```

Create a gradle project using spring init. The Application.java file.


```java
package com.demo.reactdb.project64;

import java.time.Duration;
import java.util.Optional;
import java.util.concurrent.Callable;
import java.util.concurrent.Executors;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.repository.CrudRepository;
import org.springframework.http.MediaType;
import org.springframework.lang.NonNull;
import org.springframework.stereotype.Service;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.support.TransactionTemplate;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;
import reactor.core.scheduler.Scheduler;
import reactor.core.scheduler.Schedulers;

@SpringBootApplication
@Slf4j
public class Application implements ApplicationRunner {

    @Autowired
    AppService appService;

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Override
    public void run(ApplicationArguments args) throws Exception {
        log.info("Seeding data!");

        Flux<String> names = Flux.just("raj", "david", "pam").delayElements(Duration.ofSeconds(2));
        Flux<String> colors = Flux.just("red", "blue", "green").delayElements(Duration.ofSeconds(3));
        Flux<Customer> customers = Flux.zip(names, colors).map(tupple -> {
            return new Customer(null, tupple.getT1(), tupple.getT2());
        });

        appService.deleteAll().thenMany(customers.flatMap(c -> appService.save(c))
                .thenMany(appService.findAll())).subscribe(System.out::println);
    }

}

@Configuration
class DataConfig {

    @Value("${spring.datasource.maximum-pool-size:100}")
    private int connectionPoolSize;

    @Bean
    public Scheduler jdbcScheduler() {
        return Schedulers.fromExecutor(Executors.newFixedThreadPool(connectionPoolSize));
    }

    @Bean
    public TransactionTemplate transactionTemplate(PlatformTransactionManager transactionManager) {
        return new TransactionTemplate(transactionManager);
    }
}

@Service
class AppService {

    @Autowired
    @Qualifier("jdbcScheduler")
    Scheduler jdbcScheduler;

    @Autowired
    private TransactionTemplate transactionTemplate;

    @Autowired
    AddressRepository repo;

    public Flux<Customer> findAll() {
        return asyncIterable(() -> repo.findAll().iterator());
    }

    public Mono<Optional<Customer>> findById(Long id) {
        return asyncCallable(() -> repo.findById(id));
    }

    public Mono<Customer> save(Customer customer) {
        return asyncCallable(() -> repo.save(customer));
    }

    public Mono<Void> delete(Customer customer) {
        return asyncCallable(() -> {
            repo.delete(customer);
            return null;
        });
    }

    public Mono<Void> deleteAll() {
        return asyncCallable(() -> {
            repo.deleteAll();
            return null;
        });
    }

    protected <S> Mono<S> asyncCallable(Callable<S> callable) {
        return Mono.fromCallable(callable).subscribeOn(Schedulers.parallel()).publishOn(jdbcScheduler);
    }

    protected <S> Flux<S> asyncIterable(Iterable<S> iterable) {
        return Flux.fromIterable(iterable).subscribeOn(Schedulers.parallel()).publishOn(jdbcScheduler);
    }
}

@RestController
@RequestMapping("/api")
class AppController {

    @Autowired
    AppService appService;

    @GetMapping("/all")
    public Flux<Customer> findAll() {
        return appService.findAll();
    }

    @GetMapping("/id/{customerId}")
    public Mono<Optional<Customer>> findAll(@PathVariable Long customerId) {
        return appService.findById(customerId);
    }

    @PostMapping(value = "/save", consumes = MediaType.APPLICATION_JSON_UTF8_VALUE)
    public Mono<Customer> save(@RequestBody Customer customer) {
        return appService.save(customer);
    }
}

interface AddressRepository extends CrudRepository<Customer, Long> {
}

@Data
@AllArgsConstructor
@NoArgsConstructor
@Entity(name = "customer")
class Customer {
    @Id
    @GeneratedValue
    private Long id;
    @NonNull
    private String name;
    @NonNull
    private String color;
}
```

The application.yaml file 

```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/demodb
    username: demouser
    password: demopwd
    driver-class-name: org.postgresql.Driver
    maximum-pool-size: 100
  jpa:
    database-platform: org.hibernate.dialect.PostgreSQL9Dialect
    hibernate.ddl-auto: create
    properties:
      hibernate:
        temp:
          use_jdbc_metadata_defaults: false
        jdbc:
          lob:
            non_contextual_creation: true
```

The build.gradle file

```groovy
plugins {
    id 'org.springframework.boot' version '2.1.3.RELEASE'
    id 'java'
}

apply plugin: 'io.spring.dependency-management'

group = 'com.demo.reactdb'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '1.8'

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}

repositories {
    mavenCentral()
    maven { url "https://repo.spring.io/snapshot" }
    maven { url "https://repo.spring.io/milestone" }
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-webflux'
    runtime group: 'org.postgresql', name: 'postgresql', version: '42.2.5'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

```

Run the project with "./gradlew bootRun

Get list of customers
```bash
curl -X GET \
  http://localhost:8080/api/all \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: fe758acc-0ff1-4a20-866b-2fb83e79d9e8' \
  -H 'cache-control: no-cache'

[{"id":1,"name":"raj","color":"red"},{"id":2,"name":"david","color":"blue"},{"id":3,"name":"pam","color":"green"}]
```

Get by id
```bash
curl -X GET \
  http://localhost:8080/api/id/1 \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: f4003fb3-503e-40cd-9e0f-2061cefdbc29' \
  -H 'cache-control: no-cache'

{"id":1,"name":"raj","color":"red"}
```

Save new customer
```bash
curl -X POST \
  http://localhost:8080/api/save \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 4f3f4fae-9c5f-4cc0-8b5b-ac32d38bd4ab' \
  -H 'cache-control: no-cache' \
  -d '{
    "name": "dave",
    "color": "red"
}'

```

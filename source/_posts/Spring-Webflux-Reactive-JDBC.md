---
title: Spring Webflux & Reactive JDBC
date: 2019-04-02 00:00:00
tags: spring, webflux, jdbc, reactive
---

A Webflux application integration with JDBC to allow non-blocking calls to database. R2DBC is still not production ready hence this approach should help you integrate existing relational database with webflux.

Github: [https://github.com/gitorko/project64](https://github.com/gitorko/project64)

<!-- more -->

<!-- toc -->

## Postgres Docker Instance
For development activities lets bring up a postgres server as a docker container. 

```bash
docker run -d --name postgres -p 5432:5432 -e POSTGRES_USER="demouser" -e POSTGRES_PASSWORD="demopwd" -e POSTGRES_DB="demodb" -d postgres
```

We will now connect to the db and create the table.

```bash
docker exec -it postgres psql -U demouser demodb
```
Run the sql

```sql
CREATE TABLE customer (
   id  SERIAL PRIMARY KEY,
   name VARCHAR(50) NOT NULL,
   AGE INT NOT NULL
);
```

You should be able to see your table.
```bash
demodb=# \dt
          List of relations
 Schema |   Name   | Type  |  Owner   
--------+----------+-------+----------
 public | customer | table | demouser
(1 row)
```

## Webflux Application

Spring webflux provided non-blocking support for rest api. However with R2DBC still under experimental support the following approach provides a way to make non-blocking calls to JDBC.


You can now create a gradle project using using [start.spring.io](http://start.spring.io/)

Application.java


```java
package com.demo.reactdb.project64;

import java.time.Duration;
import java.util.Optional;
import java.util.concurrent.Callable;
import java.util.concurrent.Executors;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
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

        Flux<String> names = Flux.just("raj", "david", "pam").delayElements(Duration.ofSeconds(1));
        Flux<Integer> ages = Flux.just(25, 27, 30).delayElements(Duration.ofSeconds(1));
        Flux<Customer> customers = Flux.zip(names, ages).map(tupple -> {
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
    public Mono<Optional<Customer>> findById(@PathVariable Long customerId) {
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
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    @NonNull
    private String name;
    @NonNull
    private Integer age;
}
```

The application.yaml file

```yaml
server:
  port: 8080
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/demodb
    username: demouser
    password: demopwd
    driver-class-name: org.postgresql.Driver
    maximum-pool-size: 100
  jpa:
    database-platform: org.hibernate.dialect.PostgreSQL9Dialect
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
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-webflux'
    runtime 'org.postgresql:postgresql:42.2.5'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
}

```

You can run the project

```bash
./gradlew bootRun
```

Your service should now be up

```bash
curl -X GET \
  http://localhost:8080/api/all \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 03d44176-e636-4174-a0da-b2286a3d580d' \
  -H 'cache-control: no-cache'

curl -X GET \
  http://localhost:8080/api/id/1 \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 7649705c-8b3e-4c4d-b222-fafeddcbcc1c' \
  -H 'cache-control: no-cache'

curl -X POST \
  http://localhost:8080/api/save \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 765e1c69-4328-4ff4-9418-58f9cb6d4caa' \
  -H 'cache-control: no-cache' \
  -d '{
    "name": "david",
    "age": 25
}'
```

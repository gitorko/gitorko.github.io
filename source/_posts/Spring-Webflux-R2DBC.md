---
title: Spring Webflux & R2DBC
date: 2019-04-03 00:00:00
tags: spring, webflux, reactive, r2dbc
---

A Webflux application integration with reactive R2DBC. R2DBC stands for Reactive Relational Database Connectivity, It provides a reactive driver to connect to relational database. Spring Data R2DBC makes it easy to use R2DBC libraries.

Github: [https://github.com/gitorko/project65](https://github.com/gitorko/project65)

<!-- more -->

<!-- toc -->

## Spring Data R2DBC

Spring Data R2DBC aims at being conceptually easy. In order to achieve this it does NOT offer caching, lazy loading, write behind or many other features of ORM frameworks. This makes Spring Data R2DBC a simple, limited, opinionated object mapper. Currently its use in production is not recommended. 

As of this writing only 3 relational database have r2dbc libraries
  * Postgres (io.r2dbc:r2dbc-postgresql)
  * H2 (io.r2dbc:r2dbc-h2)
  * Microsoft SQL Server (io.r2dbc:r2dbc-mssql)

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

## Webflux application

You can now create a gradle project using using [start.spring.io](http://start.spring.io/)

Application.java 

```java
package com.demo.project65;

import java.time.Duration;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.annotation.Id;
import org.springframework.data.r2dbc.config.AbstractR2dbcConfiguration;
import org.springframework.data.r2dbc.repository.config.EnableR2dbcRepositories;
import org.springframework.data.r2dbc.repository.query.Query;
import org.springframework.data.relational.core.mapping.Table;
import org.springframework.data.repository.reactive.ReactiveCrudRepository;
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import io.r2dbc.postgresql.PostgresqlConnectionConfiguration;
import io.r2dbc.postgresql.PostgresqlConnectionFactory;
import io.r2dbc.spi.ConnectionFactory;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

@SpringBootApplication
@Slf4j
public class Application implements ApplicationRunner {

    @Autowired
    CustomerRepository repo;

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
        repo.deleteAll()
                .thenMany(customers.flatMap(c -> repo.save(c))
                        .thenMany(repo.findAll())
                ).subscribe(System.out::println);
    }
}

@RestController
@RequestMapping("/api")
class AppController {

    @Autowired
    CustomerRepository repo;

    @GetMapping("/all")
    public Flux<Customer> findAll() {
        return repo.findAll();
    }

    @GetMapping("/id/{customerId}")
    public Mono<Customer> findById(@PathVariable Long customerId) {
        return repo.findById(customerId);
    }

    @PostMapping(value = "/save", consumes = MediaType.APPLICATION_JSON_UTF8_VALUE)
    public Mono<Customer> save(@RequestBody Customer customer) {
        return repo.save(customer);
    }

    @GetMapping("/find")
    public Flux<Customer> findById(@RequestParam String name, @RequestParam Integer age) {
        return repo.findByNameAndAge(name, age);
    }
}

@Configuration
@EnableR2dbcRepositories
class DatabaseConfiguration extends AbstractR2dbcConfiguration {

    @Bean
    public ConnectionFactory connectionFactory() {
        return new PostgresqlConnectionFactory(
                PostgresqlConnectionConfiguration.builder()
                        .host("localhost")
                        .port(5432)
                        .database("demodb")
                        .username("demouser")
                        .password("demopwd")
                        .build()
        );
    }

}

interface CustomerRepository extends ReactiveCrudRepository<Customer, Long> {

    @Query("select * from customer where name = $1 and age = $2")
    Flux<Customer> findByNameAndAge(String name, Integer age);
}

@Data
@AllArgsConstructor
@NoArgsConstructor
@Table("customer")
class Customer {
    @Id
    public Long id;
    public String name;
    public Integer age;
}
```

application.yaml

```yaml
server:
  port: 8080
```

You can run the project

```bash
./gradlew bootRun
```

Your service should now be up

```bash
curl -X GET \
  'http://localhost:8080/api/find?name=raj&age=25' \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 0a64b5ab-9725-41d6-8103-f7a5b5757931' \
  -H 'cache-control: no-cache'

curl -X GET \
  http://localhost:8080/api/all \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 86d0427e-5474-4d65-8f97-02dec783af1a' \
  -H 'cache-control: no-cache'

curl -X GET \
  http://localhost:8080/api/id/1 \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: b7c57690-beb7-437f-b8b8-3f15d7389feb' \
  -H 'cache-control: no-cache'

curl -X POST \
  http://localhost:8080/api/save \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: b4ba9015-e74f-43eb-87c6-2efc1711b3f7' \
  -H 'cache-control: no-cache' \
  -d '{
    "name": "jack",
    "age": 33
}'
```

## Errors
If you encounter any of the error mentioned below it could probably be because the data type in postgres cant be mapped by r2dbc.
Eg: CHAR is not supported, changing to VARCHAR will fix the issue.

org.springframework.data.mapping.MappingException: Could not read property public java.lang.String com.demo.project65.Customer.name from result set!
org.springframework.data.r2dbc.function.convert.EntityRowMapper.readFrom(EntityRowMapper.java:103) ~[spring-data-r2dbc-1.0.0.M1.jar:1.0.0.M1]
Caused by: java.lang.IllegalArgumentException: Cannot decode value of type java.lang.Object
org.springframework.data.r2dbc.function.convert.EntityRowMapper.readFrom(EntityRowMapper.java:99) ~[spring-data-r2dbc-1.0.0.M1.jar:1.0.0.M1]

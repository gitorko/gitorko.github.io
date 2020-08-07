---
title: Spring Boot Querydsl
date: 2020-08-07 00:00:00
tags:
---

Spring Boot Querydsl lets you query the database using domain specific language similar to SQL.

Github: [https://github.com/gitorko/project75](https://github.com/gitorko/project75)

<!-- more -->

<!-- toc -->

## Spring Querydsl

Lets say you used Spring Data to query the db by using spring naming convention. However if you table has 100 columns and you have to query by any column you cant write 100 functions. This is where query dsl comes into play.

## Code

Application.java

```java
package com.demo.project75;

import java.util.stream.IntStream;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

import com.querydsl.core.types.dsl.StringExpression;
import com.querydsl.core.types.dsl.StringPath;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.event.ContextRefreshedEvent;
import org.springframework.context.event.EventListener;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.querydsl.QuerydslPredicateExecutor;
import org.springframework.data.querydsl.binding.QuerydslBinderCustomizer;
import org.springframework.data.querydsl.binding.QuerydslBindings;
import org.springframework.data.querydsl.binding.QuerydslPredicate;
import org.springframework.data.querydsl.binding.SingleValueBinding;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
public class Application {

    @Autowired
    CustomerRepository customerRepository;

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @EventListener
    public void onStartSeedData(ContextRefreshedEvent event) {
        //Insert test data
        IntStream.range(0,5).forEach(i -> {
            customerRepository.save(Customer.builder()
                    .firstName("firstname_" + i)
                    .lastName("lastname " + i)
                    .age(30)
                    .email("email@email.com")
                    .build());
        });
    }
}

@Entity
@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
class Customer {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String firstName;
    private String lastName;
    private String email;
    private int age;
}

@RestController
class HomeController{

    @Autowired
    CustomerRepository customerRepository;

    @RequestMapping(method = RequestMethod.GET, value = "/users")
    public Iterable<Customer> findAllByWebQuerydsl(
            @QuerydslPredicate(root = Customer.class,
                                bindings = CustomerBinderCustomizer.class) com.querydsl.core.types.Predicate predicate) {
        return customerRepository.findAll(predicate);
    }
}

interface CustomerRepository extends JpaRepository<Customer, Long>, QuerydslPredicateExecutor<Customer> {
}

class CustomerBinderCustomizer implements QuerydslBinderCustomizer<QCustomer> {

    @Override
    public void customize(QuerydslBindings querydslBindings, QCustomer qCustomer) {
        querydslBindings.including(
                        qCustomer.id,
                        qCustomer.firstName,
                        qCustomer.lastName,
                        qCustomer.age,
                        qCustomer.email);

        // Allow case-insensitive partial searches on all strings.
        querydslBindings.bind(String.class).first((SingleValueBinding<StringPath, String>) StringExpression::containsIgnoreCase);
    }
}
```

build.gradle

```groovy
plugins {
    id 'org.springframework.boot' version '2.3.2.RELEASE'
    id 'io.spring.dependency-management' version '1.0.9.RELEASE'
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
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    compile 'com.querydsl:querydsl-apt:4.3.1'
    compile 'com.querydsl:querydsl-core:4.3.1'
    compile 'com.querydsl:querydsl-jpa:4.3.1'
    compile 'org.postgresql:postgresql'
    compile 'com.h2database:h2'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
    annotationProcessor 'com.querydsl:querydsl-apt:4.3.1:jpa'
    annotationProcessor  group: 'javax.persistence'   , name: 'javax.persistence-api'
    annotationProcessor  group: 'javax.annotation'    , name: 'javax.annotation-api'
}
```

Run the project

```bash
./gradlew bootRun
```

You can now search based on all the columns of the db and get the response. 

[http://localhost:8080/users?age=30](http://localhost:8080/users?age=30)
[http://localhost:8080/users?age=firstname_0](http://localhost:8080/users?age=firstname_0)

## References

Spring Query DSL : [http://www.querydsl.com/static/querydsl/latest/reference/html/ch02.html](http://www.querydsl.com/static/querydsl/latest/reference/html/ch02.html)

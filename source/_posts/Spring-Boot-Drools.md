---
title: Spring Boot & Drools
date: 2019-03-29 00:00:00
tags: drools,spring boot,spring
---

A Spring boot application integration Drools. Drools is a Business Rule Management System (BRMS). Business & Non-Technical users can write the rules in a format that is easy to understand and plug it into drools engine. These rules/facts are processed to produce results. Cost of changing the rules is low.

Github: [https://github.com/gitorko/project63](https://github.com/gitorko/project63)

<!-- more -->

<!-- toc -->

## Drools

You can now create a gradle project using using [start.spring.io](http://start.spring.io/)

```java
package com.demo.project63;

import org.kie.api.KieServices;
import org.kie.api.builder.KieBuilder;
import org.kie.api.builder.KieFileSystem;
import org.kie.api.builder.KieModule;
import org.kie.api.runtime.KieContainer;
import org.kie.api.runtime.KieSession;
import org.kie.internal.io.ResourceFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

import lombok.extern.slf4j.Slf4j;

@SpringBootApplication
@Slf4j
public class Application implements ApplicationRunner {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Autowired
    private KieContainer kContainer;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        KieSession kieSession = kContainer.newKieSession();
        Product product = new Product();
        product.setType("gold");
        kieSession.insert(product);
        kieSession.fireAllRules();
        kieSession.dispose();
        log.info("!! Discount !! " + product.getDiscount());
    }

    @Bean
    public KieContainer kieContainer() {

        KieServices kieServices = KieServices.Factory.get();
        KieFileSystem kieFileSystem = kieServices.newKieFileSystem();
        kieFileSystem.write(ResourceFactory.newClassPathResource("product-discount.drl"));
        KieBuilder kieBuilder = kieServices.newKieBuilder(kieFileSystem);
        kieBuilder.buildAll();
        KieModule kieModule = kieBuilder.getKieModule();
        return kieServices.newKieContainer(kieModule.getReleaseId());
    }

}
```

Create Product.java

```java
package com.demo.project63;

import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
public class Product {
    private String type;
    private int discount;
}
```

application.yaml

```yaml
server:
  port: 8080
spring:
  main:
    web-application-type: none
```

Create a drools file product-discount.drl in the resources folder.

```java
import com.demo.project63.Product;

rule "Offer for Diamond"
	when 
		productObject: Product(type=="diamond")
	then
		productObject.setDiscount(15);
	end
rule "Offer for Gold"
	when 
		productObject: Product(type=="gold")
	then
		productObject.setDiscount(25);
	end

```

build.gradle file

```groovy
plugins {
    id 'org.springframework.boot' version '2.1.3.RELEASE'
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
    implementation 'org.springframework.boot:spring-boot-starter'
    implementation 'org.drools:drools-core:7.19.0.Final'
    implementation 'org.drools:drools-decisiontables:7.19.0.Final'
    implementation 'org.drools:drools-compiler:7.19.0.Final'
    implementation 'org.kie:kie-ci:7.19.0.Final'
    implementation 'org.kie:kie-spring:7.19.0.Final'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
}

```

You can run the project

```bash
./gradlew bootRun
```

!! Discount !! 25

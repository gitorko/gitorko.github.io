---
title: Spring Boot & Drools
date: 2019-03-29 00:00:00
tags: drools,spring boot,spring
---

A Spring boot application integration with Drools. Drools is a Business Rule Management System (BRMS). Business & Non-Technical users can write the rules in a format that is easy to understand and plug it into drools engine. These rules/facts are processed to produce results. Cost of changing the rules is low.

Github: [https://github.com/gitorko/project63](https://github.com/gitorko/project63)

<!-- more -->

<!-- toc -->

## Drools

You can now create a gradle project using using [start.spring.io](http://start.spring.io/)

```java
package com.demo.project63;

import java.util.Arrays;
import java.util.Collections;
import java.util.HashMap;
import java.util.Map;

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


        //Simple
        KieSession kieSession1 = kContainer.newKieSession();
        Product p1 = new Product();
        p1.setType("desktop");
        p1.setRegions(Collections.EMPTY_MAP);
        p1.setManufacturers(Collections.emptyList());
        kieSession1.insert(p1);
        kieSession1.fireAllRules();
        kieSession1.dispose();
        log.info("Discount on {} is {}", p1.getType(), p1.getDiscount());

        //Iterates a Map.
        KieSession kieSession2 = kContainer.newKieSession();
        Product p2 = new Product();
        p2.setType("laptop");
        Map<String,String> r2 = new HashMap<>();
        r2.put("region1","A");
        r2.put("region2","B");
        r2.put("region3","C");
        p2.setRegions(r2);
        p2.setManufacturers(Collections.emptyList());
        kieSession2.insert(p2);
        kieSession2.fireAllRules();
        kieSession2.dispose();
        log.info("Discount on {} is {}", p2.getType(), p2.getDiscount());

        //Iterates List
        KieSession kieSession3 = kContainer.newKieSession();
        Product p3 = new Product();
        p3.setType("keyboard");
        p3.setRegions(Collections.EMPTY_MAP);
        p3.setManufacturers(Arrays.asList("Company1", "Company2"));
        kieSession3.insert(p3);
        kieSession3.fireAllRules();
        kieSession3.dispose();
        log.info("Discount on {} is {}", p3.getType(), p3.getDiscount());

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

import java.util.List;
import java.util.Map;

import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
public class Product {
    private String type;
    private int discount;
    private Map<String, String> regions;
    private List<String> manufacturers;
}
```

Create a drools file product-discount.drl in the resources folder.

```java
import com.demo.project63.Product;
import java.util.Map;

rule "Discount Based on Product"
	when
		$product: Product(type == "desktop")
	then
	    System.out.println("Discount provided for product");
		$product.setDiscount(15);
	end


rule "Discount Based on Store A,B,C"
	when 
		$product: Product($regions : regions)
		$region: Map() from $regions
		$entry: Map.Entry( $key: key, $val: value ) from $region.entrySet()
		eval($val.equals("A") || $val.equals("B") || $val.equals("C"))
	then
	    System.out.println("Discount provided for product in specific region");
		$product.setDiscount(10);
	end

rule "Discount Based Manufacturer"
	when
		$product: Product($manufacturers : manufacturers)
		$manufacturer: String() from $manufacturers
		eval($manufacturer.equals("Company1") || $manufacturer.equals("Company2"))
	then
	    System.out.println("Discount provided for manufacturer");
		$product.setDiscount(5);
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

Run the project

```bash
./gradlew bootRun
```

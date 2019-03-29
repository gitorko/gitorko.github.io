---
title: Drools Spring Integration
date: 2019-03-29 00:00:00
tags: drools,spring
---

We will learn how to integrate spring and drools.

<!-- more -->

<!-- toc -->

## Drools

Drools is a Business Rule Management System (BRMS). Business & Non-Technical users can write the rules in a format that is easy to understand and plug it into drools engine. These rules/facts are processed to produce results. Cost of changing the rules is low.

### Setup

Create a maven project.

```java
package com.demo.drools;

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

@SpringBootApplication
public class Main implements ApplicationRunner {

    @Autowired
    private KieContainer kContainer;

    public static void main(String[] args) {
        SpringApplication.run(Main.class, args);
    }

    @Override
    public void run(ApplicationArguments args) throws Exception {
        KieSession kieSession = kContainer.newKieSession();
        Product product = new Product();
        product.setType("gold");
        kieSession.insert(product);
        kieSession.fireAllRules();
        kieSession.dispose();
        System.out.println("!! Discount !! " + product.getDiscount());
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
package com.demo.drools;

import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
public class Product {
    private String type;
    private int discount;
}
```

Create a drools file product-discount.drl in the resources folder.

```java
import com.demo.drools.Product;

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

Update pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.2.RELEASE</version>
        <relativePath/>
    </parent>
    <groupId>com.demo.drools</groupId>
    <artifactId>drool-test</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
        <drools-version>7.19.0.Final</drools-version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.drools</groupId>
            <artifactId>drools-core</artifactId>
            <version>${drools-version}</version>
        </dependency>
        <dependency>
            <groupId>org.drools</groupId>
            <artifactId>drools-decisiontables</artifactId>
            <version>${drools-version}</version>
        </dependency>
        <dependency>
            <groupId>org.drools</groupId>
            <artifactId>drools-compiler</artifactId>
            <version>${drools-version}</version>
        </dependency>
        <dependency>
            <groupId>org.kie</groupId>
            <artifactId>kie-ci</artifactId>
            <version>${drools-version}</version>
        </dependency>
        <dependency>
            <groupId>org.kie</groupId>
            <artifactId>kie-spring</artifactId>
            <version>${drools-version}</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

Run the program you should see an output
!! Discount !! 25

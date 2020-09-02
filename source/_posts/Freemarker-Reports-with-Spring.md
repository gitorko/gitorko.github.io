---
title: Freemarker Reports with Spring
date: 2020-08-28 00:00:00
tags:
- freemarker report
categories:
- [Reports]
---

Using freemarker templates we can generate basic html reports that can be downloaded. 

Github: [https://github.com/gitorko/project69](https://github.com/gitorko/project69)

<!-- more -->

<!-- toc -->

## Freemarker template reports 

We will generate a single html file report using freemarker template and provide a rest url to download the report.

## Code

Application.java

```java
package org.gitokro.project69;

import java.io.ByteArrayInputStream;
import java.io.StringWriter;
import java.util.Date;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Random;
import java.util.stream.Collectors;
import java.util.stream.IntStream;

import freemarker.template.Configuration;
import freemarker.template.Template;
import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.core.io.InputStreamResource;
import org.springframework.http.HttpHeaders;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}

@RestController
@Slf4j
class HomeController {

    @GetMapping("/report")
    public ResponseEntity<InputStreamResource> getReport() throws Exception {
        log.info("Generating report!");
        String htmlReport = this.generateHtmlReport();
        ByteArrayInputStream bis = new ByteArrayInputStream(htmlReport.getBytes());
        return ResponseEntity.ok()
                .contentType(MediaType.APPLICATION_OCTET_STREAM)
                .header(HttpHeaders.CONTENT_DISPOSITION, "attachment; filename=\"myreport.htm\"")
                .body(new InputStreamResource(bis));
    }

    private String generateHtmlReport() throws Exception {
        Configuration cfg = new Configuration(Configuration.VERSION_2_3_30);
        cfg.setClassForTemplateLoading(this.getClass(), "/");
        cfg.setDefaultEncoding("UTF-8");
        Template template = cfg.getTemplate("templates/my-report.ftl");
        List<Employee> employees = getEmployeeData();
        Map<String, Object> templateData = new HashMap<>();
        templateData.put("reportTitle", "Company Employee Report");
        templateData.put("employees", employees);
        StringWriter out = new StringWriter();
        template.process(templateData, out);
        return out.toString();
    }

    private List<Employee> getEmployeeData() {
        //Sample Data
        Random random = new Random();
        return IntStream.range(0, 150)
                .mapToObj(i -> Employee.builder()
                        .name("Name " + i)
                        .age(random.nextInt(65 - 20) + 20)
                        .dob(new Date())
                        .salary(40000 + (100000 - 40000) * random.nextDouble())
                        .build())
                .collect(Collectors.toList());
    }
}
```

Employee.java

```java
package org.gitokro.project69;

import java.util.Date;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

@Builder
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Employee {
    String name;
    Integer age;
    Date dob;
    Double salary;
}
```

my-report.ftl

```html
<html>

<head>
    <title>Employee Report</title>
    <style>
        table {
            font-family: arial, sans-serif;
            border-collapse: collapse;
            width: 100%;
        }

        td,
        th {
            border: 1px solid #DDDDDD;
            text-align: left;
            padding: 8px;
        }

        th {
            background-color: #CCCCCC;
        }

        p.yellow {
            color: #FFDC0B;
            font-weight: bold;
        }

        p.green {
            color: #2F8400;
            font-weight: bold;
        }
    </style>
</head>

<body>
<h3>${(reportTitle)!"Default Title"}</h3>
<br/>

<h4>Employee Details</h4>
<table>
    <tr>
        <th>Id</th>
        <th>Name</th>
        <th>Age</th>
        <th>Dob</th>
        <th>Salary</th>
    </tr>
    <#assign empCounter=1>
    <#list employees as empObj>
        <tr>
            <td>${empCounter}</td>
            <td>
                <a href="#">${empObj.name}</a>
            </td>
            <td>
                ${empObj.age}
            </td>
            <td>
                ${empObj.dob?date}
            </td>
            <td>
                <#if empObj.salary gt 50000>
                    <p class="green">
                        ${empObj.salary}
                    </p>
                <#else>
                    <p class="yellow">
                        ${empObj.salary}
                    </p>
                </#if>
            </td>
        </tr>
        <#assign empCounter=empCounter+1>
    </#list>
</table>
<h2>Total Employees: ${employees?size}</h2>
</body>
</html>
```

build.gradle

```groovy
plugins {
    id 'org.springframework.boot' version '2.3.3.RELEASE'
    id 'io.spring.dependency-management' version '1.0.10.RELEASE'
    id 'java'
}

group = 'org.gitokro'
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
    implementation 'org.springframework.boot:spring-boot-starter-freemarker'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
}
```

Run the project and access the url in a browser and the report will be downloaded.

[http://localhost:8080/report](http://localhost:8080/report)

{% asset_img image01.png %}

## References

[https://freemarker.apache.org/](https://freemarker.apache.org/)

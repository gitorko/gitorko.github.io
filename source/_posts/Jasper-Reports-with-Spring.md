---
title: Jasper Reports with Spring
date: 2020-08-29 00:00:00
tags:
- jasper report
categories:
- [Reports]
---

Using JasperReports an open source Java reporting tool we will generate a pdf report by passing data to the report template. 

Github: [https://github.com/gitorko/project70](https://github.com/gitorko/project70)

<!-- more -->

<!-- toc -->

## Jasper Report 

JasperReports is an open source java reporting engine. It can generate different types of reports in this example we look at generating a pdf report with data passed from the java layer. To generate the jasper template you will need to download and install jasper studio. Jasper report also comes with a server but for this demo you dont need to install it.

[https://community.jaspersoft.com/download](https://community.jaspersoft.com/download)

## Code

{% ghcode https://github.com/gitorko/project70/blob/master/src/main/java/org/gitorko/project70/Application.java 25 65 %}

&nbsp;

{% ghcode https://github.com/gitorko/project70/blob/master/src/main/resources/EmployeeReports.jrxml %}

Run the project to generate the EmployeeReports.pdf file.

```bash
./gradlew bootRun
```

To create the jasper report template file you can use jasper studio

{% youtube uVAQ577rKIE %}

## References

[https://community.jaspersoft.com/](https://community.jaspersoft.com/)

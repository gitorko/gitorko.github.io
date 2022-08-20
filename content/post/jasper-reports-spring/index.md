---
title: 'Jasper Reports with Spring'
description: 'Jasper Reports with Spring'
summary: 'Generate a pdf report using jasper reports'
date: '2020-08-30'
aliases: [/jasper-reports-spring/]
author: 'Arjun Surendra'
categories: [Reports]
tags: [jasper-report]
toc: true
---

Generate a pdf report using jasper reports

Github: [https://github.com/gitorko/project70](https://github.com/gitorko/project70)

## Jasper Report 

JasperReports is an open source java reporting engine. It can generate different types of reports in this example we look at generating a pdf report with data passed from the java layer. 
To generate the jasper template you will need to download and install jasper studio. Jasper report also comes with a server but for this demo you dont need to install it.

[https://community.jaspersoft.com/download](https://community.jaspersoft.com/download)

## Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project70/main/src/main/java/com/demo/project70/Main.java" >}}
{{< ghcode "https://raw.githubusercontent.com/gitorko/project70/main/src/main/resources/EmployeeReports.jrxml" >}}

Run the project to generate the EmployeeReports.pdf file.

```bash
./gradlew bootRun
```

To create the jasper report template file you can use jasper studio

{{< youtube uVAQ577rKIE >}}

## References

[https://community.jaspersoft.com/](https://community.jaspersoft.com/)

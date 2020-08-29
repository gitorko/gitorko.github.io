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

Application.java

```java
package org.gitorko.project70;

import java.io.File;
import java.io.FileOutputStream;
import java.io.OutputStream;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import net.sf.jasperreports.engine.JREmptyDataSource;
import net.sf.jasperreports.engine.JasperCompileManager;
import net.sf.jasperreports.engine.JasperExportManager;
import net.sf.jasperreports.engine.JasperFillManager;
import net.sf.jasperreports.engine.JasperPrint;
import net.sf.jasperreports.engine.JasperReport;
import net.sf.jasperreports.engine.data.JRBeanCollectionDataSource;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application implements CommandLineRunner {

    static final String fileName = "src/main/resources/EmployeeReports.jrxml";
    static final String outFile = "EmployeeReports.pdf";

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Override
    public void run(String... args) throws Exception {
        List<Employee> employeeList = new ArrayList<Employee>();
        Map<String, Object> parameter = new HashMap<String, Object>();

        employeeList.add(new Employee(1, "Jack Ryan", 100.0));
        employeeList.add(new Employee(2, "Cathy Mueller", 130.0));
        employeeList.add(new Employee(3, "Matice", 90.0));

        parameter.put("employeeDataSource", new JRBeanCollectionDataSource(employeeList));
        parameter.put("title", "Employee Report");

        JasperReport jasperDesign = JasperCompileManager.compileReport(fileName);
        JasperPrint jasperPrint = JasperFillManager.fillReport(jasperDesign, parameter, new JREmptyDataSource());

        File file = new File(outFile);
        OutputStream outputSteam = new FileOutputStream(file);
        JasperExportManager.exportReportToPdfStream(jasperPrint, outputSteam);

        System.out.println("Report Generated!");
    }

    @Data
    @AllArgsConstructor
    @NoArgsConstructor
    public class Employee {
        private int id;
        private String name;
        private Double salary;
    }
}
```

application.properties
```
spring.main.web-application-type=none
```

EmployeeReports.jrxml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- Created with Jaspersoft Studio version 6.14.0.final using JasperReports Library version 6.14.0-2ab0d8625be255bf609c78e1181801213e51db8f  -->
<jasperReport xmlns="http://jasperreports.sourceforge.net/jasperreports" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://jasperreports.sourceforge.net/jasperreports http://jasperreports.sourceforge.net/xsd/jasperreport.xsd" name="EmployeeReports" pageWidth="595" pageHeight="842" columnWidth="555" leftMargin="20" rightMargin="20" topMargin="20" bottomMargin="20" uuid="78066d92-d5f8-4a86-bde2-9824be76fbdf">
    <property name="com.jaspersoft.studio.data.defaultdataadapter" value="One Empty Record"/>
    <style name="Table_TH" mode="Opaque" backcolor="#F0F8FF">
        <box>
            <pen lineWidth="0.5" lineColor="#000000"/>
            <topPen lineWidth="0.5" lineColor="#000000"/>
            <leftPen lineWidth="0.5" lineColor="#000000"/>
            <bottomPen lineWidth="0.5" lineColor="#000000"/>
            <rightPen lineWidth="0.5" lineColor="#000000"/>
        </box>
    </style>
    <style name="Table_CH" mode="Opaque" backcolor="#BFE1FF">
        <box>
            <pen lineWidth="0.5" lineColor="#000000"/>
            <topPen lineWidth="0.5" lineColor="#000000"/>
            <leftPen lineWidth="0.5" lineColor="#000000"/>
            <bottomPen lineWidth="0.5" lineColor="#000000"/>
            <rightPen lineWidth="0.5" lineColor="#000000"/>
        </box>
    </style>
    <style name="Table_TD" mode="Opaque" backcolor="#FFFFFF">
        <box>
            <pen lineWidth="0.5" lineColor="#000000"/>
            <topPen lineWidth="0.5" lineColor="#000000"/>
            <leftPen lineWidth="0.5" lineColor="#000000"/>
            <bottomPen lineWidth="0.5" lineColor="#000000"/>
            <rightPen lineWidth="0.5" lineColor="#000000"/>
        </box>
    </style>
    <subDataset name="employeeDataSet" uuid="9651295c-429d-420d-9e5b-42055e73efea">
        <property name="com.jaspersoft.studio.data.defaultdataadapter" value="One Empty Record"/>
        <queryString>
            <![CDATA[]]>
        </queryString>
        <field name="id" class="java.lang.Integer"/>
        <field name="name" class="java.lang.String"/>
        <field name="salary" class="java.lang.Double"/>
    </subDataset>
    <parameter name="title" class="java.lang.String"/>
    <parameter name="employeeDataSource" class="net.sf.jasperreports.engine.data.JRBeanCollectionDataSource"/>
    <queryString>
        <![CDATA[]]>
    </queryString>
    <background>
        <band splitType="Stretch"/>
    </background>
    <title>
        <band height="79" splitType="Stretch">
            <textField>
                <reportElement x="250" y="20" width="100" height="30" uuid="2f6bf147-59e5-4468-84b4-bf89a58646ef"/>
                <textElement>
                    <font size="12"/>
                </textElement>
                <textFieldExpression><![CDATA[$P{title}]]></textFieldExpression>
            </textField>
        </band>
    </title>
    <pageHeader>
        <band height="35" splitType="Stretch"/>
    </pageHeader>
    <columnHeader>
        <band height="61" splitType="Stretch"/>
    </columnHeader>
    <detail>
        <band height="246" splitType="Stretch">
            <componentElement>
                <reportElement x="180" y="30" width="200" height="200" uuid="63d288f4-369c-494f-85b0-f0108b22765e">
                    <property name="com.jaspersoft.studio.layout" value="com.jaspersoft.studio.editor.layout.VerticalRowLayout"/>
                    <property name="com.jaspersoft.studio.table.style.table_header" value="Table_TH"/>
                    <property name="com.jaspersoft.studio.table.style.column_header" value="Table_CH"/>
                    <property name="com.jaspersoft.studio.table.style.detail" value="Table_TD"/>
                </reportElement>
                <jr:table xmlns:jr="http://jasperreports.sourceforge.net/jasperreports/components" xsi:schemaLocation="http://jasperreports.sourceforge.net/jasperreports/components http://jasperreports.sourceforge.net/xsd/components.xsd">
                    <datasetRun subDataset="employeeDataSet" uuid="6fe4bdfe-9dd2-4d5e-b94b-c951044cc1cf">
                        <dataSourceExpression><![CDATA[$P{employeeDataSource}]]></dataSourceExpression>
                    </datasetRun>
                    <jr:column width="66" uuid="4ba6dc06-71ed-4eac-925f-3940726ecfa8">
                        <jr:tableHeader style="Table_TH" height="30"/>
                        <jr:tableFooter style="Table_TH" height="30"/>
                        <jr:columnHeader style="Table_CH" height="30">
                            <staticText>
                                <reportElement x="0" y="0" width="66" height="30" uuid="2fa1464e-80ba-4599-b563-c9f922460fc6"/>
                                <text><![CDATA[id]]></text>
                            </staticText>
                        </jr:columnHeader>
                        <jr:columnFooter style="Table_CH" height="30"/>
                        <jr:detailCell style="Table_TD" height="30">
                            <textField>
                                <reportElement x="0" y="0" width="66" height="30" uuid="fcae93e6-a2e8-44d2-b05a-18cf222b2580"/>
                                <textFieldExpression><![CDATA[$F{id}]]></textFieldExpression>
                            </textField>
                        </jr:detailCell>
                    </jr:column>
                    <jr:column width="66" uuid="a9abc185-068b-44b0-ba3e-c52793b91a90">
                        <jr:tableHeader style="Table_TH" height="30"/>
                        <jr:tableFooter style="Table_TH" height="30"/>
                        <jr:columnHeader style="Table_CH" height="30">
                            <staticText>
                                <reportElement x="0" y="0" width="66" height="30" uuid="02aa9397-a647-477d-ae35-d87a6d2c7bc1"/>
                                <text><![CDATA[name]]></text>
                            </staticText>
                        </jr:columnHeader>
                        <jr:columnFooter style="Table_CH" height="30"/>
                        <jr:detailCell style="Table_TD" height="30">
                            <textField>
                                <reportElement x="0" y="0" width="66" height="30" uuid="f13bc7e1-3206-4e65-9377-9e488acec741"/>
                                <textFieldExpression><![CDATA[$F{name}]]></textFieldExpression>
                            </textField>
                        </jr:detailCell>
                    </jr:column>
                    <jr:column width="66" uuid="d0e58992-65cf-48e0-a03a-3fc71f88c1da">
                        <jr:tableHeader style="Table_TH" height="30"/>
                        <jr:tableFooter style="Table_TH" height="30"/>
                        <jr:columnHeader style="Table_CH" height="30">
                            <staticText>
                                <reportElement x="0" y="0" width="66" height="30" uuid="cb76f4a3-45a1-4b8c-9e41-e4e586c79496"/>
                                <text><![CDATA[salary]]></text>
                            </staticText>
                        </jr:columnHeader>
                        <jr:columnFooter style="Table_CH" height="30"/>
                        <jr:detailCell style="Table_TD" height="30">
                            <textField>
                                <reportElement x="0" y="0" width="66" height="30" uuid="76129bc3-7141-42f8-9059-3fb60b79ec4d"/>
                                <textFieldExpression><![CDATA[$F{salary}]]></textFieldExpression>
                            </textField>
                        </jr:detailCell>
                    </jr:column>
                </jr:table>
            </componentElement>
        </band>
    </detail>
    <columnFooter>
        <band height="45" splitType="Stretch"/>
    </columnFooter>
    <pageFooter>
        <band height="54" splitType="Stretch"/>
    </pageFooter>
    <summary>
        <band height="42" splitType="Stretch"/>
    </summary>
</jasperReport>
```

build.gradle

```groovy
plugins {
    id 'org.springframework.boot' version '2.3.3.RELEASE'
    id 'io.spring.dependency-management' version '1.0.10.RELEASE'
    id 'java'
}

group = 'org.gitorko'
version = '1.0.0'
sourceCompatibility = '11'

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}

repositories {
    mavenCentral()
    maven { url "http://jaspersoft.jfrog.io/jaspersoft/third-party-ce-artifacts/" }

}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
    compile 'net.sf.jasperreports:jasperreports:6.14.0'
    compile 'com.lowagie:itext:2.1.7'
}
```

Run the project to generate the EmployeeReports.pdf file.

To create the jasper report template file you can use jasper studio

{% youtube uVAQ577rKIE %}

## References

[https://community.jaspersoft.com/](https://community.jaspersoft.com/)

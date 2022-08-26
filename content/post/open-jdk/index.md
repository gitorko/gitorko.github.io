---
title: 'OpenJDK'
description: 'OpenJDK'
summary: 'OpenJDK distributions'
date: '2019-04-17'
aliases: [/open-jdk/]
author: 'Arjun Surendra'
categories: [JDK]
tags: [azul, openjdk, correto]
toc: true
---

Oracle JDK requires licensing for use in commercial products. OpenJDK is free, We look at the various distributions of OpenJDK that are available. 

## Distributions

Oracle made JDK 17 free for all again, however if you hold existing Oracle Master Agreement ("OMA") there is still some confusion.

1. Azul Platform Core [https://www.azul.com/downloads/](https://www.azul.com/downloads/)
2. Amazon Corretto [https://aws.amazon.com/corretto/](https://aws.amazon.com/corretto/)
3. Red Hat OpenJDK [https://developers.redhat.com/products/openjdk/download](https://developers.redhat.com/products/openjdk/download)
4. Adoptium (AdoptOpenJDK) [https://adoptium.net/](https://adoptium.net/)

## JNLP 

In OpenJDK, javaws is missing, this is need to open old JNLP files if the project has it. You can download the IceTea binaries to get JNLP working in amazon correto.

Edit $icedTeaDirectory\bin\javaws.bat and replace set INST_JAVA_HOME= with set INST_JAVA_HOME=”$correttoDirectory” to use Corretto Java runtime

[http://icedtea.wildebeest.org/download/icedtea-web-binaries/1.8/](http://icedtea.wildebeest.org/download/icedtea-web-binaries/1.8/)

Download and unzip the zip file. Add the $icedTeaDirectory\bin\ path to the path environment variable and invoke

```bash
javaw.bat client.jnlp
```

## Versions

To deal with different versions of java you can use jenv

```bash
brew install jenv
```

Set the jdk version either globally or locally

```bash
jenv versions
jenv version
jenv global 11.0
jenv local 17.0
```

## Tools

You can also download the various tools needed to work with java

1. VisualVM - [https://visualvm.github.io/](https://visualvm.github.io/)
2. Memory Analyzer - [https://www.eclipse.org/mat/](https://www.eclipse.org/mat/)
3. Mission Control - [https://www.azul.com/products/components/azul-mission-control/](https://www.azul.com/products/components/azul-mission-control/)

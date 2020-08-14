---
title: Amazon Correto Open JDK
date: 2019-04-16 00:00:00
tags: 
- correto
- openjdk
categories:
- [Java]
---

With the introduction of licensing on java JRE, use for embedded devices or use of commercial features may require a license from Oracle. Open JDK provided by Amazon looks like the path forward.

<!-- more -->

<!-- toc -->

With Amazon backing the Open JDK and providing free multiplatform OpenJDK which they claim is already running on their production application, It looks like the way forward for java. Having used Amazon Correto for a month now i feel confident of its usage in docker container and production ready apps. Amazon Correto provides OpenJDK 8 and OpenJDK 11 binaries.

[https://aws.amazon.com/corretto/](https://aws.amazon.com/corretto/)

One of the caveat was missing javaws which is need to open JNLP files. However you can download the IceTea binaries to get JNLP working in amazon correto.

Edit $icedTeaDirectory\bin\javaws.bat and replace set INST_JAVA_HOME= with set INST_JAVA_HOME=”$correttoDirectory” to use Corretto Java runtime

[http://icedtea.wildebeest.org/download/icedtea-web-binaries/1.8/](http://icedtea.wildebeest.org/download/icedtea-web-binaries/1.8/)

Download and unzip the zip file. Edit $icedTeaDirectory\bin\javaws.bat and set INST_JAVA_HOME= to use Corretto Java home directory. Place the $icedTeaDirectory\bin\ on the path environment variable and invoke

```bash
javaw.bat client.jnlp
```
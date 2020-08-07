---
title: Spring Cloud Vault
date: 2020-08-06 00:00:00
tags:
---

HashiCorp vault secures, stores and tightly controls access to tokens, passwords, certificates, API keys and other secrets. Spring cloud vault lets you integrate with vault.

Github: [https://github.com/gitorko/project74](https://github.com/gitorko/project74)

<!-- more -->

<!-- toc -->

## Spring Cloud Vault

To install vault on mac run the command, for other OS download and install vault.

```bash
brew install vault
```

Start the dev server

```bash
vault server -dev -log-level=INFO -dev-root-token-id=00000000-0000-0000-0000-000000000000
```

Once vault is up, insert some values

```bash
export VAULT_ADDR=http://localhost:8200
export VAULT_SKIP_VERIFY=true
export VAULT_TOKEN=00000000-0000-0000-0000-000000000000
vault kv put secret/myapp username=demouser password=demopassword myKey=foobar group.key1=val1 group.key2=val2
```

You can login to vault UI with token '00000000-0000-0000-0000-000000000000'

Vault UI: [http://127.0.0.1:8200/](http://127.0.0.1:8200/)

{% asset_img image01.png %}

Now lets access the values from spring boot

## Code

You can now create a gradle project using using [start.spring.io](http://start.spring.io/)

Application.java

```java
package com.demo.project74;

import lombok.Data;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.vault.core.VaultKeyValueOperationsSupport;
import org.springframework.vault.core.VaultOperations;
import org.springframework.vault.core.VaultSysOperations;
import org.springframework.vault.core.VaultTemplate;
import org.springframework.vault.core.VaultTransitOperations;
import org.springframework.vault.support.VaultMount;
import org.springframework.vault.support.VaultResponse;

@SpringBootApplication
@Slf4j
@EnableConfigurationProperties(MySecrets.class)
public class Application implements CommandLineRunner {

    @Autowired
    private VaultTemplate vaultTemplate;

    @Value("${username}")
    private String myusername;

    private final MySecrets mySecrets;

    @Autowired
    private VaultOperations operations;

    public Application(MySecrets mySecrets) {
        this.mySecrets = mySecrets;
    }

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Override
    public void run(String... strings) {

        log.info("Value injected via @Value : {}", myusername);

        log.info("Value injected via class: {}", mySecrets.getKey1());

        //Reading directly.
        VaultResponse response = vaultTemplate.opsForKeyValue("secret",
                VaultKeyValueOperationsSupport.KeyValueBackend.KV_2).get("myapp");
        log.info("Value of myKey: {} ", response.getData().get("myKey"));

        //Writing new values to different path.
        VaultTransitOperations transitOperations = vaultTemplate.opsForTransit();
        VaultSysOperations sysOperations = vaultTemplate.opsForSys();
        if (!sysOperations.getMounts().containsKey("transit/")) {
            sysOperations.mount("transit", VaultMount.create("transit"));
            transitOperations.createKey("foo-key");
        }

        // Encrypt a plain-text value
        String ciphertext = transitOperations.encrypt("foo-key", "Secure message");
        log.info("Encrypted value: {}", ciphertext);

        // Decrypt
        String plaintext = transitOperations.decrypt("foo-key", ciphertext);
        log.info("Decrypted value: {}", plaintext);
    }

}

@Data
@ConfigurationProperties("group")
class MySecrets{
    String key1;
    String key2;
}
```

application.yaml
```json
username: test
password: test@123
spring:
    application:
        name: myapp
```

bootstrap.yaml

```json
spring:
  cloud:
    # Configuration for a vault server running in dev mode
    vault:
      scheme: http
      host: localhost
      port: 8200
      connection-timeout: 5000
      read-timeout: 15000
      authentication: TOKEN
      token: 00000000-0000-0000-0000-000000000000
      kv:
        enabled=true:
      application-name: myapp
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

ext {
    springCloudVersion = 'Greenwich.SR2'
}

dependencyManagement {
    imports {
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:Hoxton.SR7"
    }
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter'
    compile 'org.springframework.cloud:spring-cloud-starter-vault-config'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
}
```

Run the project

```bash
./gradlew bootRun
```

You should now see the values being fetched from vault.

```
Started Application in 1.187 seconds (JVM running for 1.727)
Value injected via @Value : demouser
Value injected via class: val1
Value of myKey: foobar 
Encrypted value: vault:v1:TEwBNmAkKMG3RwWGebJy/zRkxnMPpowPpYG5FPiIjSav4gEzRrAX3swZ
Decrypted value: Secure message
Shutting down ExecutorService
```

Few other vault command to try

```bash
vault kv get secret/myapp
vault kv put -cas=1 secret/myapp username=demo-user
vault kv delete secret/myapp
```

## References

Spring Cloud Vault : [https://cloud.spring.io/spring-cloud-vault/reference/html/](https://cloud.spring.io/spring-cloud-vault/reference/html/)
Vault: [https://www.vaultproject.io/](https://www.vaultproject.io/)

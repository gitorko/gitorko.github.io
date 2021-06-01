---
title: Spring Cloud Vault
date: 2020-08-06 00:00:00
tags:
- spring cloud vault
categories:
- [SpringBoot]
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

{% ghcode https://github.com/gitorko/project74/blob/master/src/main/java/com/demo/project74/Application.java %}

{% ghcode https://github.com/gitorko/project74/blob/master/src/main/resources/application.yaml %}

{% ghcode https://github.com/gitorko/project74/blob/master/src/main/resources/bootstrap.yaml %}

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
vault kv put secret/myapp username=demo-user
vault kv delete secret/myapp
```

## References

Spring Cloud Vault : [https://cloud.spring.io/spring-cloud-vault/reference/html/](https://cloud.spring.io/spring-cloud-vault/reference/html/)
Vault: [https://www.vaultproject.io/](https://www.vaultproject.io/)

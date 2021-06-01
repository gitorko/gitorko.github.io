---
title: Spring Boot Property Refresh
date: 2020-08-08 00:00:00
tags:
- properties
categories:
- [Spring]
---

HashiCorp vault is useful in centralizing all the properties and passwords. Sometimes you need your running application to detect the changed property value in order to provide a toggle on/off feature.

Github: [https://github.com/gitorko/project76](https://github.com/gitorko/project76)

<!-- more -->

<!-- toc -->

## Spring Properties Refresh

To provide a toggle feature you can use the @RefreshScope annotation and trigger a refresh using spring actuator.

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
vault kv put secret/myapp check.flag=true
```

You can login to vault UI with token '00000000-0000-0000-0000-000000000000'

Vault UI: [http://127.0.0.1:8200/](http://127.0.0.1:8200/)

Now lets access the values from spring boot and then change the value and have the app detect it while its still running.

## Code

{% ghcode https://github.com/gitorko/project76/blob/master/src/main/java/com/demo/project76/Application.java %}

{% ghcode https://github.com/gitorko/project76/blob/master/src/main/resources/application.yaml %}

{% ghcode https://github.com/gitorko/project76/blob/master/src/main/resources/bootstrap.yaml %}

Run the project

```bash
./gradlew bootRun
```

You can now hit the greet url to see a 'Good Morning' response.

```bash
curl --location --request GET 'localhost:8080/greet'
```

Now lets change the flag to false in vault

```bash
vault kv put secret/myapp check.flag=false
```

In order for the values to be refreshed by spring context you need to make a call to actuator api

```bash
curl --location --request POST 'http://localhost:8080/actuator/refresh'
```

Now the values will be refreshed and hitting the greet url will show 'Good Bye' response.

```bash
curl --location --request GET 'localhost:8080/greet'
```

## References

Spring Cloud Vault : [https://cloud.spring.io/spring-cloud-vault/reference/html/](https://cloud.spring.io/spring-cloud-vault/reference/html/)
Vault: [https://www.vaultproject.io/](https://www.vaultproject.io/)

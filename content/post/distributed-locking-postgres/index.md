---
title: 'Distributed Locking - Postgres'
description: 'Distributed Locking - Postgres'
summary: 'Distributed Locking - Postgres'
date: '2024-02-15'
aliases: [/distributed-locking-postgres/]
author: 'Arjun Surendra'
categories: [Postgres, Spring]
tags: [distributed-lock, postgres, liquibase]
toc: true
---

Spring boot application with distributed locking using postgres

Github: [https://github.com/gitorko/project05](https://github.com/gitorko/project05)

## Distributed Locking

When there are many service running and need to acquire a lock to run a critical region of the code there is contention.

1. Use **OptimisticLocking** to avoid 2 threads from acquiring the same lock
2. Use **UNIQUE** constraint to ensure same lock is present only once in the db.
3. Even if the server crashes/dies the locks should be auto released and not held forever or should not require manual intervention for cleanup.
4. Use virtual threads cleanup locks after duration is completed.
5. Only the node/server that acquired the lock can release the lock.

### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project05/main/src/main/java/com/demo/project05/service/InternalLockService.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project05/main/src/main/java/com/demo/project05/service/DistributedLockService.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project05/main/src/main/java/com/demo/project05/controller/LockController.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project05/main/src/main/resources/db/changelog/changes/001-initial-schema.sql" >}}

### Postman

![](img01.png)

Import the postman collection to postman

[Postman Collection](https://raw.githubusercontent.com/gitorko/project05/main/postman/Project05.postman_collection.json)

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project05/main/README.md" >}}

## References

[https://ignite.apache.org/](https://ignite.apache.org/)
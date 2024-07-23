---
title: 'Message Queue - Postgres'
description: 'Message Queue - Postgres'
summary: 'Message Queue - Postgres'
date: '2024-01-01'
aliases: [/message-queue-postgres/]
author: 'Arjun Surendra'
categories: [Spring, JPA, PostgreSQL, Queue]
tags: [jdbc, task, queue, liquibase]
toc: true
draft: false
---

Message Queue implementation using PostgreSQL

Github: [https://github.com/gitorko/project81](https://github.com/gitorko/project81)

## Message Queue

PostgreSQL can be used a messaging queue, it also offers features like LISTEN/NOTIFY which make it a suitable to support message queue.

**Advantages**

1. Reuse existing infrastructure - Use an existing database keeping the tech stack simple.
2. Low messages throughput - Not every system needs high volume of messages to process per second.
3. Persistent Store - You can query the db to check the messages if they are processed and manually trigger re-queue.

This command notifies the channel of a new message in the queue

```sql
NOTIFY new_task_channel, 'New task added';
```

This command listens for these notifications

```sql
LISTEN new_task_channel;
```

You also need to lock the row being read to avoid the same row from being updated by 2 different transactions

`FOR UPDATE` clause -  This clause locks the selected rows for update. This prevents other transactions from modifying these rows until the current transaction is completed (committed or rolled back)
`SKIP LOCKED` clause - This clause tells the database to skip rows that are already locked by another transaction. Instead of waiting for the lock to be released

**Disadvantages**

1. Missing notifications if a worker is disconnected.
2. Row-level locking is needed to prevent multiple workers from picking up the same message.

### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project81/main/src/main/java/com/demo/project81/config/ListenerConfiguration.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project81/main/src/main/java/com/demo/project81/service/NotificationHandler.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project81/main/src/main/java/com/demo/project81/service/NotifierService.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project81/main/src/main/java/com/demo/project81/service/TaskService.java" >}}

### Postman

Import the postman collection to postman

[Postman Collection](https://raw.githubusercontent.com/gitorko/project81/main/postman/Project81.postman_collection.json)

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project81/main/README.md" >}}

## References

[https://www.postgresql.org/docs/current/sql-listen.html](https://www.postgresql.org/docs/current/sql-listen.html)

[https://www.postgresql.org/docs/current/sql-notify.html](https://www.postgresql.org/docs/current/sql-notify.html)

[https://www.postgresql.org/docs/current/sql-lock.html](https://www.postgresql.org/docs/current/sql-lock.html)
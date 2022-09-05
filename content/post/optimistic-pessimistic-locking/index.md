---
title: 'Optimistic vs Pessimistic Locking'
description: 'Optimistic vs Pessimistic Locking'
summary: 'Optimistic vs Pessimistic Locking implementation'
date: '2019-12-24'
aliases: [/optimistic-pessimistic-locking/]
author: 'Arjun Surendra'
categories: [Locking]
tags: [optimistic-locking, pessimistic-locking, JPA]
toc: true
---

When an app is deployed on more than one server how to you ensure that 2 threads dont modify the same record in db? If the operation was performed on a single JVM you could look at locking but since there are many jvm the locking has to be done at database level.

Github: [https://github.com/gitorko/project67](https://github.com/gitorko/project67)

## Locking

Lets say we have a ticket booking service with a table holding all the free tickets and multiple servers running our app. How do we ensure that 2 users cant book the same seat? Since the app runs on different JVM we cant syncronize or use jvm locks.

Hibernate provides two approaches to handle concurrency at database level:

1. Pessimistic Locking - The lock is now applied by the database at row level or table level. If the lock is a WRITE lock it prevents other threads from modifying the data. eg: SELECT * from TABLE where id = 1 for update;
2. Optimistic Locking - A version field is introduced to the database table, The JPA ensures that version check is done before saving data, if the version has changed the update will throw Error. Scalability is high with this approach.

### Pessimistic locking

Three Pessimistic LockModeTypes are supported in JPA.

1. PESSIMISTIC_READ - Rows are locked and can be read by other transactions, but they cannot be deleted or modified. PESSIMISTIC_READ guarantees repeatable reads.
2. PESSIMISTIC_WRITE - Rows are locked and cannot be read, modified or deleted by other transactions. For PESSIMISTIC_WRITE no phantom reads can occur and access to data must be serialized.
3. PESSIMISTIC_FORCE_INCREMENT - Rows are locked and cannot be modified or deleted. For versioned entities, their version number is incremented as soon as the query executes.

{{< ghcode "https://raw.githubusercontent.com/gitorko/project67/main/src/main/java/com/demo/project67/pessimistic/PessimisticMain.java" >}}

Run the code and you will see that only one person is able to book the ticket. Notice the 'for update' in the sql query that is fired which will lock the row.

```bash
Booking seat: 1 By: Joe
Booking seat: 1 By: Jack
Hibernate: select ticket0_.id as id1_0_, ticket0_.booked_by as booked_b2_0_, ticket0_.on_day as on_day3_0_, ticket0_.seat_number as seat_num4_0_ from ticket ticket0_ where ticket0_.seat_number=? and ticket0_.on_day=? for update
Hibernate: select ticket0_.id as id1_0_, ticket0_.booked_by as booked_b2_0_, ticket0_.on_day as on_day3_0_, ticket0_.seat_number as seat_num4_0_ from ticket ticket0_ where ticket0_.seat_number=? and ticket0_.on_day=? for update
Hibernate: update ticket set booked_by=?, on_day=?, seat_number=? where id=?
Booking for Jack success: true
Booking for Joe success: false
Hibernate: select ticket0_.id as id1_0_, ticket0_.booked_by as booked_b2_0_, ticket0_.on_day as on_day3_0_, ticket0_.seat_number as seat_num4_0_ from ticket ticket0_
Ticket(id=1, seatNumber=1, onDay=2020-08-17, bookedBy=Jack)
Ticket(id=2, seatNumber=2, onDay=2020-08-17, bookedBy=null)
Ticket(id=3, seatNumber=3, onDay=2020-08-17, bookedBy=null)
```

### Optimistic locking

{{< ghcode "https://raw.githubusercontent.com/gitorko/project67/main/src/main/java/com/demo/project67/optimistic/OptimisticMain.java" >}}

Run the code and you will see that only one person is able to book the ticket. Notice the exception of 'StaleObjectStateException' thrown.

```
Booking seat: 1 By: Joe
Booking seat: 1 By: Jack
Hibernate: select ticket0_.id as id1_0_, ticket0_.booked_by as booked_b2_0_, ticket0_.on_day as on_day3_0_, ticket0_.seat_number as seat_num4_0_, ticket0_.version as version5_0_ from ticket ticket0_ where ticket0_.seat_number=? and ticket0_.on_day=?
Hibernate: select ticket0_.id as id1_0_, ticket0_.booked_by as booked_b2_0_, ticket0_.on_day as on_day3_0_, ticket0_.seat_number as seat_num4_0_, ticket0_.version as version5_0_ from ticket ticket0_ where ticket0_.seat_number=? and ticket0_.on_day=?
Hibernate: select ticket0_.id as id1_0_0_, ticket0_.booked_by as booked_b2_0_0_, ticket0_.on_day as on_day3_0_0_, ticket0_.seat_number as seat_num4_0_0_, ticket0_.version as version5_0_0_ from ticket ticket0_ where ticket0_.id=?
Hibernate: select ticket0_.id as id1_0_0_, ticket0_.booked_by as booked_b2_0_0_, ticket0_.on_day as on_day3_0_0_, ticket0_.seat_number as seat_num4_0_0_, ticket0_.version as version5_0_0_ from ticket ticket0_ where ticket0_.id=?
Hibernate: update ticket set booked_by=?, on_day=?, seat_number=?, version=? where id=? and version=?
Hibernate: update ticket set booked_by=?, on_day=?, seat_number=?, version=? where id=? and version=?
Booking for Joe success: true
Hibernate: select ticket0_.id as id1_0_0_, ticket0_.booked_by as booked_b2_0_0_, ticket0_.on_day as on_day3_0_0_, ticket0_.seat_number as seat_num4_0_0_, ticket0_.version as version5_0_0_ from ticket ticket0_ where ticket0_.id=?
Object of class [com.demo.project67.optimistic.Ticket] with identifier [1]: optimistic locking failed; nested exception is org.hibernate.StaleObjectStateException: Row was updated or deleted by another transaction (or unsaved-value mapping was incorrect) : [com.demo.project67.optimistic.Ticket#1]
Booking for Jack success: false
Hibernate: select ticket0_.id as id1_0_, ticket0_.booked_by as booked_b2_0_, ticket0_.on_day as on_day3_0_, ticket0_.seat_number as seat_num4_0_, ticket0_.version as version5_0_ from ticket ticket0_
Ticket(id=1, seatNumber=1, onDay=2020-08-17, bookedBy=Joe, version=1)
Ticket(id=2, seatNumber=2, onDay=2020-08-17, bookedBy=null, version=0)
Ticket(id=3, seatNumber=3, onDay=2020-08-17, bookedBy=null, version=0)
```

{{< ghcode "https://raw.githubusercontent.com/gitorko/project67/main/src/main/resources/application.yaml" >}}

## References

Spring Data JPA : [https://spring.io/projects/spring-data-jpa](https://spring.io/projects/spring-data-jpa)

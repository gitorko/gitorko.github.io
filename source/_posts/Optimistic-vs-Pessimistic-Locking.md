---
title: Optimistic vs Pessimistic Locking
date: 2019-12-23 00:00:00
tags:
- optimistic locking
- pessimistic locking
- ticket booking
categories:
- [JPA]
- [Design Pattern]
---

When an app is deployed on more than one server how to you ensure that 2 threads dont modify the same record in db? If the operation was performed on a single JVM you could look at locking but since there are many jvm the locking has to be done at database level.

<!-- more -->

<!-- toc -->

## Ticket booking

Lets say we have a ticket booking service with a table holding all the free tickets and multiple servers running our app. How do we ensure that 2 users cant book the same seat? Since the app runs on different JVM we cant syncronize or use jvm locks.

Hibernate provides two approaches to handle concurrency at database level:

1. Pessimistic Approach - The lock is now applied by the database at row level or table level. If the lock is a WRITE lock it prevents other threads from modifying the data. 
2. Optimistic Approach - A version field is introduced to the database table, The JPA ensures that version check is done before saving data. Scalability is high with this approach.

### Pessimistic locking

Three Pessimistic LockModeTypes are supported in JPA.

1. PESSIMISTIC_READ - Rows are locked and can be read by other transactions, but they cannot be deleted or modified. PESSIMISTIC_READ guarantees repeatable reads.
2. PESSIMISTIC_WRITE - Rows are locked and cannot be read, modified or deleted by other transactions. For PESSIMISTIC_WRITE no phantom reads can occur and access to data must be serialized.
3. PESSIMISTIC_FORCE_INCREMENT - Rows are locked and cannot be modified or deleted. For versioned entities, their version number is incremented as soon as the query executes.

PessimisticMain.java

```java
package com.demo.project67.pessimistic;

import java.util.Date;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.LockModeType;
import javax.persistence.Temporal;
import javax.persistence.TemporalType;
import javax.transaction.Transactional;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Lock;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Repository;

@Configuration
@EnableAutoConfiguration
@ComponentScan(basePackages = "com.demo.project67.pessimistic")
public class PessimisticMain {

    @Autowired
    MyService myService;

    ExecutorService pool = Executors.newCachedThreadPool();

    public static void main(String[] args) {
        SpringApplication.run(PessimisticMain.class, args);
    }

    @Bean
    public CommandLineRunner commandLineRunner() {
        return args -> {
            myService.seedData();
            pool.submit(() -> {
                Boolean status = myService.bookSeat(1, "Joe");
                System.out.println("Booking for Joe success: "+ status);
            });
            pool.submit(() -> {
                Boolean status = myService.bookSeat(1, "Jack");
                System.out.println("Booking for Jack success: "+ status);
            });
            pool.shutdown();
            pool.awaitTermination(60, TimeUnit.SECONDS);
            myService.showData();
        };
    }
}

@Entity
@Data
@Builder
@AllArgsConstructor
@NoArgsConstructor
class Ticket {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private Integer seatNumber;
    @Temporal(TemporalType.DATE)
    private Date onDay;
    private String bookedBy;
}

@Repository
interface TicketRepository extends JpaRepository<Ticket, Long> {

    @Lock(LockModeType.PESSIMISTIC_WRITE)
    Ticket findBySeatNumberAndOnDay(Integer seatNumber, Date onDay);

}

@Component
class MyService {
    @Autowired
    TicketRepository ticketRepository;

    @Transactional
    public Boolean bookSeat(Integer seatNumber, String personName) {
        try {
            System.out.println("Booking seat: " + seatNumber + " By: " + personName);
            Ticket seat = ticketRepository.findBySeatNumberAndOnDay(seatNumber, new Date());
            if (seat.getBookedBy() == null) {
                seat.setBookedBy(personName);
                ticketRepository.save(seat);
                return true;
            } else {
                return false;
            }
        } catch (Exception ex) {
            System.out.println(ex.getMessage());
        }
        return false;
    }

    public void showData() {
        ticketRepository.findAll().forEach(e -> {
            System.out.println(e);
        });
    }

    public void seedData() {
        ticketRepository.deleteAll();
        for (int i = 1; i <= 3; i++) {
            ticketRepository.save(Ticket.builder().seatNumber(i).onDay(new Date()).build());
        }
    }
}
```

Run the code and you will see that only one person is able to book the ticket. Notice the 'for update' in the sql query that is fired which will lock the row.

```
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

OptimisticMain.java

```java
package com.demo.project67.optimistic;

import java.util.Date;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Temporal;
import javax.persistence.TemporalType;
import javax.persistence.Version;
import javax.transaction.Transactional;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Repository;

@Configuration
@EnableAutoConfiguration
@ComponentScan(basePackages = "com.demo.project67.optimistic")
public class OptimisticMain {

    @Autowired
    MyService myService;

    ExecutorService pool = Executors.newCachedThreadPool();

    public static void main(String[] args) {
        SpringApplication.run(OptimisticMain.class, args);
    }

    @Bean
    public CommandLineRunner commandLineRunner() {
        return args -> {
            myService.seedData();
            pool.submit(() -> {
                Boolean status = myService.bookSeat(1, "Joe");
                System.out.println("Booking for Joe success: "+ status);
            });
            pool.submit(() -> {
                Boolean status = myService.bookSeat(1, "Jack");
                System.out.println("Booking for Jack success: "+ status);
            });
            pool.shutdown();
            pool.awaitTermination(60, TimeUnit.SECONDS);
            myService.showData();
        };
    }
}

@Entity
@Data
@Builder
@AllArgsConstructor
@NoArgsConstructor
class Ticket {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private Integer seatNumber;
    @Temporal(TemporalType.DATE)
    private Date onDay;
    private String bookedBy;
    @Version
    private int version;
}

@Repository
interface TicketRepository extends JpaRepository<Ticket, Long> {
    @Transactional
    Ticket findBySeatNumberAndOnDay(Integer seatNumber, Date onDay);
}

@Component
class MyService {
    @Autowired
    TicketRepository ticketRepository;

    public Boolean bookSeat(Integer seatNumber, String personName) {
        try {
            System.out.println("Booking seat: " + seatNumber + " By: " + personName);
            Ticket seat = ticketRepository.findBySeatNumberAndOnDay(seatNumber, new Date());
            if (seat.getBookedBy() == null) {
                seat.setBookedBy(personName);
                ticketRepository.save(seat);
                return true;
            } else {
                return false;
            }
        } catch (Exception ex) {
            System.out.println(ex.getMessage());
        }
        return false;
    }

    public void showData() {
        ticketRepository.findAll().forEach(e -> {
            System.out.println(e);
        });
    }

    public void seedData() {
        ticketRepository.deleteAll();
        for (int i = 1; i <= 3; i++) {
            ticketRepository.save(Ticket.builder().seatNumber(i).onDay(new Date()).build());
        }
    }
}
```

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

application.yaml

```yaml
spring:
  main:
    web-application-type: none
  jpa:
    hibernate.ddl-auto: create-drop
    show-sql: true
```

build.gradle

```groovy
plugins {
    id 'org.springframework.boot' version '2.3.3.RELEASE'
    id 'io.spring.dependency-management' version '1.0.10.RELEASE'
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

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    compileOnly 'org.projectlombok:lombok'
    runtimeOnly 'com.h2database:h2'
    annotationProcessor 'org.projectlombok:lombok'
}
```
---
title: Spring JPA N+1
date: 2019-06-10 00:00:00
tags:
- n+1 problem
- jpa
categories:
- [JPA]
---

The N+1 query problem occurs when the framework executes N additional SQL statements to fetch the same data that could have been retrieved when executing the primary SQL query.

Github: [https://github.com/gitorko/project66](https://github.com/gitorko/project66)

<!-- more -->

<!-- toc -->

## N+1 problem

N+1 problem is a performance issue in ORM that fires multiple select queries 
By default fetch is FetchType.LAZY in hibernate, changing to FetchType.EAGER wont guarantee a fix either also eager fetch will fetch more data than needed.
The @ManyToOne and @OneToOne associations use FetchType.EAGER by default.

## Code

Application.java

```java
package com.demo.project66;

import java.util.Arrays;
import java.util.List;
import javax.persistence.CascadeType;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.OneToMany;
import javax.transaction.Transactional;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.data.jpa.repository.EntityGraph;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.CrudRepository;
import org.springframework.stereotype.Component;

@SpringBootApplication
public class Application {

    @Autowired
    MyService myService;

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Bean
    public CommandLineRunner commandLineRunner() {
        return args -> {
            myService.seedData();
            myService.getData();
        };
    }

}

@Entity
@Data
@Builder
@AllArgsConstructor
@NoArgsConstructor
class Post {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String title;
    @OneToMany(cascade = { CascadeType.ALL })
    private List<PostComment> comments;
}

@Entity
@Data
@Builder
@AllArgsConstructor
@NoArgsConstructor
class PostComment {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String comment;
}

interface PostRepository extends CrudRepository<Post, Long> {
    @Query("SELECT p FROM Post p LEFT JOIN FETCH p.comments")
    List<Post> findAllNoNplus1();

//    @EntityGraph(attributePaths = {"comments"})
//    List<Post> findAll();
}

@Component
class MyService {
    @Autowired
    PostRepository postRepository;

    @Transactional
    public void getData() {
        Iterable<Post> all = postRepository.findAll();
//        Iterable<Post> all = postRepository.findAllNoNplus1();
        for (Post post : all) {
            System.out.println(post.getTitle());
            List<PostComment> comments = post.getComments();
            for (PostComment comment : comments) {
                System.out.println(comment);
            }
        }
    }

    public void seedData() {
        for (int i = 1; i <= 5; i++) {
            List<PostComment> comments = Arrays.asList(
                    PostComment.builder().comment("Comment 1 for " + i).build(),
                    PostComment.builder().comment("Comment 2 for " + i).build(),
                    PostComment.builder().comment("Comment 3 for " + i).build()
            );
            postRepository.save(Post.builder().title("My Post " + i).comments(comments).build());
        }

    }
}
```

application.yaml
```json
spring:
  main:
    web-application-type: none
  jpa:
    hibernate:
      ddl-auto: create-drop
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

Run the project

```bash
./gradlew bootRun
```

You will see a sql query being fired to fetch each post comment object.

```
Hibernate: select post0_.id as id1_0_, post0_.title as title2_0_ from post post0_
My Post 1
Hibernate: select comments0_.post_id as post_id1_1_0_, comments0_.comments_id as comments2_1_0_, postcommen1_.id as id1_2_1_, postcommen1_.comment as comment2_2_1_ from post_comments comments0_ inner join post_comment postcommen1_ on comments0_.comments_id=postcommen1_.id where comments0_.post_id=?
PostComment(id=2, comment=Comment 1 for 1)
PostComment(id=3, comment=Comment 2 for 1)
PostComment(id=4, comment=Comment 3 for 1)
My Post 2
Hibernate: select comments0_.post_id as post_id1_1_0_, comments0_.comments_id as comments2_1_0_, postcommen1_.id as id1_2_1_, postcommen1_.comment as comment2_2_1_ from post_comments comments0_ inner join post_comment postcommen1_ on comments0_.comments_id=postcommen1_.id where comments0_.post_id=?
PostComment(id=6, comment=Comment 1 for 2)
PostComment(id=7, comment=Comment 2 for 2)
PostComment(id=8, comment=Comment 3 for 2)
My Post 3
Hibernate: select comments0_.post_id as post_id1_1_0_, comments0_.comments_id as comments2_1_0_, postcommen1_.id as id1_2_1_, postcommen1_.comment as comment2_2_1_ from post_comments comments0_ inner join post_comment postcommen1_ on comments0_.comments_id=postcommen1_.id where comments0_.post_id=?
PostComment(id=10, comment=Comment 1 for 3)
PostComment(id=11, comment=Comment 2 for 3)
PostComment(id=12, comment=Comment 3 for 3)
My Post 4
Hibernate: select comments0_.post_id as post_id1_1_0_, comments0_.comments_id as comments2_1_0_, postcommen1_.id as id1_2_1_, postcommen1_.comment as comment2_2_1_ from post_comments comments0_ inner join post_comment postcommen1_ on comments0_.comments_id=postcommen1_.id where comments0_.post_id=?
PostComment(id=14, comment=Comment 1 for 4)
PostComment(id=15, comment=Comment 2 for 4)
PostComment(id=16, comment=Comment 3 for 4)
My Post 5
Hibernate: select comments0_.post_id as post_id1_1_0_, comments0_.comments_id as comments2_1_0_, postcommen1_.id as id1_2_1_, postcommen1_.comment as comment2_2_1_ from post_comments comments0_ inner join post_comment postcommen1_ on comments0_.comments_id=postcommen1_.id where comments0_.post_id=?
PostComment(id=18, comment=Comment 1 for 5)
PostComment(id=19, comment=Comment 2 for 5)
PostComment(id=20, comment=Comment 3 for 5)

```

Now use the 'findAllNoNplus1' function you will see that only 1 query is fired. You can also use the @EntityGraph function to achieve the same result.

```
Hibernate: select post0_.id as id1_0_0_, postcommen2_.id as id1_2_1_, post0_.title as title2_0_0_, postcommen2_.comment as comment2_2_1_, comments1_.post_id as post_id1_1_0__, comments1_.comments_id as comments2_1_0__ from post post0_ left outer join post_comments comments1_ on post0_.id=comments1_.post_id left outer join post_comment postcommen2_ on comments1_.comments_id=postcommen2_.id
My Post 1
PostComment(id=2, comment=Comment 1 for 1)
PostComment(id=3, comment=Comment 2 for 1)
PostComment(id=4, comment=Comment 3 for 1)
My Post 1
PostComment(id=2, comment=Comment 1 for 1)
PostComment(id=3, comment=Comment 2 for 1)
PostComment(id=4, comment=Comment 3 for 1)
My Post 1
PostComment(id=2, comment=Comment 1 for 1)
PostComment(id=3, comment=Comment 2 for 1)
PostComment(id=4, comment=Comment 3 for 1)
My Post 2
PostComment(id=6, comment=Comment 1 for 2)
PostComment(id=7, comment=Comment 2 for 2)
PostComment(id=8, comment=Comment 3 for 2)
My Post 2
PostComment(id=6, comment=Comment 1 for 2)
PostComment(id=7, comment=Comment 2 for 2)
PostComment(id=8, comment=Comment 3 for 2)
My Post 2
PostComment(id=6, comment=Comment 1 for 2)
PostComment(id=7, comment=Comment 2 for 2)
PostComment(id=8, comment=Comment 3 for 2)
My Post 3
PostComment(id=10, comment=Comment 1 for 3)
PostComment(id=11, comment=Comment 2 for 3)
PostComment(id=12, comment=Comment 3 for 3)
My Post 3
PostComment(id=10, comment=Comment 1 for 3)
PostComment(id=11, comment=Comment 2 for 3)
PostComment(id=12, comment=Comment 3 for 3)
My Post 3
PostComment(id=10, comment=Comment 1 for 3)
PostComment(id=11, comment=Comment 2 for 3)
PostComment(id=12, comment=Comment 3 for 3)
My Post 4
PostComment(id=14, comment=Comment 1 for 4)
PostComment(id=15, comment=Comment 2 for 4)
PostComment(id=16, comment=Comment 3 for 4)
My Post 4
PostComment(id=14, comment=Comment 1 for 4)
PostComment(id=15, comment=Comment 2 for 4)
PostComment(id=16, comment=Comment 3 for 4)
My Post 4
PostComment(id=14, comment=Comment 1 for 4)
PostComment(id=15, comment=Comment 2 for 4)
PostComment(id=16, comment=Comment 3 for 4)
My Post 5
PostComment(id=18, comment=Comment 1 for 5)
PostComment(id=19, comment=Comment 2 for 5)
PostComment(id=20, comment=Comment 3 for 5)
My Post 5
PostComment(id=18, comment=Comment 1 for 5)
PostComment(id=19, comment=Comment 2 for 5)
PostComment(id=20, comment=Comment 3 for 5)
My Post 5
PostComment(id=18, comment=Comment 1 for 5)
PostComment(id=19, comment=Comment 2 for 5)
PostComment(id=20, comment=Comment 3 for 5)
```

## References

[https://vladmihalcea.com/n-plus-1-query-problem](https://vladmihalcea.com/n-plus-1-query-problem)

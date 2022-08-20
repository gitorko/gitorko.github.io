---
title: 'Spring JPA N+1'
description: 'Spring JPA N+1'
summary: 'The N+1 query problem occurs when the framework executes N additional SQL statements to fetch the same data that could have been retrieved when executing the primary SQL query.'
date: '2019-06-11'
aliases: [/spring-jpa-n-plus-1/]
author: 'Arjun Surendra'
categories: [Spring, JPA]
tags: [spring, jpa]
toc: true
---

The N+1 query problem occurs when the framework executes N additional SQL statements to fetch the same data that could have been retrieved when executing the primary SQL query.

Github: [https://github.com/gitorko/project66](https://github.com/gitorko/project66)

## N+1 problem

N+1 problem is a performance issue in ORM that fires multiple select queries 
By default fetch is FetchType.LAZY in hibernate, changing to FetchType.EAGER wont guarantee a fix either also eager fetch will fetch more data than needed.
The @ManyToOne and @OneToOne associations use FetchType.EAGER by default.

## Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project66/main/src/main/java/com/demo/project66/Main.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project66/main/src/main/resources/application.yaml" >}}


## Testing

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

Now use the 'findAllFixed' method you will see that only 1 query is fired. You can also use the @EntityGraph function to achieve the same result.

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

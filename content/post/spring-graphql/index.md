---
title: 'Spring - GraphQL'
description: 'Spring - GraphQL'
summary: 'Spring Boot integration with GraphQL'
date: '2022-07-20'
aliases: [/spring-graphql/]
author: 'Arjun Surendra'
categories: [GraphQL]
tags: [spring, spring-boot, graphql, pagination]
toc: true
---

GraphQL is a query language that offers an alternative model to developing APIs instead of REST, SOAP or gRPC. It allows partial fetch of data, you can use a single endpoint to fetch different formats of data.  

With REST, the shape of the response is decided by the server, every endpoint returns a fixed structure and the client has to work with whatever comes back, over-fetching fields it doesn't need or under-fetching and having to call a second endpoint to get the rest. GraphQL flips that around, the server exposes a strongly typed schema describing every type, field and relationship in the system, and the client sends a query describing exactly the shape of the response it wants. The server walks the schema and resolves only the fields that were asked for.

A few things fall out of that model:

* **Single endpoint** - Unlike REST where you keep adding endpoints as requirements grow, GraphQL exposes one endpoint (`/graphql`) for every query and mutation. What changes between requests is the query document, not the URL.
* **Strongly typed schema** - Every field has a type (`String`, `Int`, `ID`, a custom object type, etc.), so the contract between client and server is explicit and can be validated before a query ever hits a resolver.
* **Introspection** - Because the schema is typed and published, tools like GraphiQL can query the schema itself to auto-generate docs, auto-complete queries and validate them client side.
* **No API versioning** - Fields can be added to a type without breaking older clients since a client only receives the fields it asked for. Deprecated fields are marked `@deprecated` instead of bumping a version number.
* **Aggregation over multiple sources** - A single query can pull together data that would otherwise need multiple REST calls, since the resolvers for different fields can independently reach into different services, databases or repositories.

The trade-off is that the server now has to do more work per request, resolving a graph of fields instead of returning a canned response, and it opens up problems that don't really exist in REST, like the N+1 problem covered further down.

Github: [https://github.com/gitorko/project96](https://github.com/gitorko/project96)

## Spring Boot GraphQL

Lets say you have a rest api that returns customer profile, the customer profile has 200+ fields, so a mobile device may not need all the fields, it may need may be 5 fields like name, address etc. Requesting a big payload over wire is costly.
So now you end up writing a rest endpoint that returns just the 5 fields. This can become overwhelming when the requirements increase and you end up creating different endpoint for such requirement.
In GraphQL you define a schema and let the user/consumer decide which fields they want to fetch. 

Before GraphQL 1.0 was Released spring had to extend the classes GraphQLMutationResolver, GraphQLQueryResolver. Its no longer required.

GraphQLMutationResolver -> @MutationMapping

GraphQLQueryResolver -> @QueryMapping

The code uses Extended Scalars for graphql-java to support Date and other type objects in GraphQL
The code shows how pagination can be done in GraphQL 

### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project96/main/src/main/java/com/demo/project96/controller/QueryController.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project96/main/src/main/java/com/demo/project96/controller/MutationController.java" >}}

The schema for GraphQL. The ! simply tells us that you can always expect a value back and will never need to check for null.

{{< ghcode "https://raw.githubusercontent.com/gitorko/project96/main/src/main/resources/graphql/post.graphqls" >}}

GraphQL accepts only one root Query and one root Mutation types, To keep the logic in different files we extend the Query and Mutation types.

{{< ghcode "https://raw.githubusercontent.com/gitorko/project96/main/src/main/resources/graphql/comment.graphqls" >}}

The key terminologies in GraphQL are

* Query: Used to read data
* Mutation: Used to create, update and delete data
* Subscription: Similar to a query allowing you to fetch data from the server. Subscriptions offer a long-lasting operation that can change their result over time.

### The N+1 Problem

`QueryController.findAllComments()` calls out that returning `commentRepository.findAll()` directly would cause an N+1 problem, one query to fetch all comments, followed by one additional query per comment to lazily load its post. With a handful of comments that's not noticeable, but with a thousand comments that's a thousand and one round trips to the database for a single GraphQL request.

The project works around it with a hand-written `JOIN FETCH` query in `CommentRepository`:

{{< ghcode "https://raw.githubusercontent.com/gitorko/project96/main/src/main/java/com/demo/project96/repo/CommentRepository.java" >}}

That works well when you know upfront which relations a query needs, but it doesn't scale to arbitrary nested queries, if `Post` itself had a lazy `author` relation, or if comments were fetched through several different queries, you'd need a hand-written fetch join for every path.

The idiomatic GraphQL fix is a `DataLoader`. Instead of resolving `Comment.post` field-by-field per comment, a batch loader collects all the post IDs requested across a single GraphQL execution and fetches them in one query, then hands each comment its post from the batch. Spring for GraphQL wraps this behind `@BatchMapping`, batching every `Comment.post` field resolution requested in an execution into a single call, regardless of which query triggered it, so it composes across `findAllComments`, `findCommentById`, `findCommentsByPostId` etc. without a separate hand-written query for each one. See the [Spring for GraphQL batch mapping docs](https://docs.spring.io/spring-graphql/reference/controllers.html#controllers.schema-mapping.batch) for the exact API.

### Error Handling

By default, an exception thrown from a resolver (like the `RuntimeException("Post not found!")` thrown in `MutationController.createComment()`) is masked and returned to the client as a generic error:

```json
{
  "errors": [
    { "message": "INTERNAL_ERROR for 9cf1eed9-c977-9be7-4767-461b7c45622c", "extensions": { "classification": "INTERNAL_ERROR" } }
  ]
}
```

That's a safe default, it avoids leaking stack traces or internal messages to a client, but it also means every failure looks the same, whether it's a missing post, a validation failure or an unhandled bug. Spring for GraphQL lets you register a `DataFetcherExceptionResolver` bean that inspects the exception and returns a classified `GraphQLError` (e.g. `NOT_FOUND` for a missing entity) instead of the generic `INTERNAL_ERROR`, falling back to the default masking for anything it doesn't recognise. See the [Spring for GraphQL exception handling docs](https://docs.spring.io/spring-graphql/reference/request-execution.html#execution.exceptions) for the exact API.

### Bruno

Import the [Bruno](https://www.usebruno.com/) collection to try out the requests.

[Bruno Collection](https://github.com/gitorko/project96/tree/main/bruno/Project96)

A few sample requests to hit the endpoint at `http://localhost:8080/api/graphql`:

Query all posts:

```graphql
query {
  findAllPosts {
    id
    header
    createdBy
    createdDt
  }
}
```

Query posts with pagination:

```graphql
query {
  findAllPostsPage(page: 0, size: 10) {
    posts {
      id
      header
      createdBy
    }
    totalElements
    totalPages
    currentPage
    size
  }
}
```

Create a post:

```graphql
mutation {
  createPost(header: "Hello world", createdBy: "John") {
    id
  }
}
```

Create a comment on a post:

```graphql
mutation {
  createComment(message: "comment1", createdBy: "John", postId: 1) {
    id
    message
    createdBy
  }
}
```

Or hit it directly with curl:

```bash
curl -X POST http://localhost:8080/api/graphql \
  -H "Content-Type: application/json" \
  -d '{"query": "query { findAllPosts { id header createdBy createdDt } }"}'
```

### Testing

Spring for GraphQL ships `GraphQlTester`, a fluent client for firing GraphQL documents at your app and asserting on the JSON response by path, without hand-rolling JSON parsing. Bound to a running server via `HttpGraphQlTester`, the queries and mutations from this project are tested like this:

{{< ghcode "https://raw.githubusercontent.com/gitorko/project96/main/src/test/java/com/demo/project96/controller/QueryControllerGraphQlTest.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project96/main/src/test/java/com/demo/project96/controller/MutationControllerGraphQlTest.java" >}}

The same `.path(...)` assertions work for mutations, and `.errors()` lets you assert on the error path without the test caring what shape the successful response would have been. Repository-level logic (custom `@Query` methods, pagination, cascades) is better covered with a focused `@DataJpaTest` against a real database instead, since GraphQL tests should be asserting on the API contract, not on JPA/Hibernate behaviour:

{{< ghcode "https://raw.githubusercontent.com/gitorko/project96/main/src/test/java/com/demo/project96/repo/CommentRepositoryTest.java" >}}

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project96/main/README.md" >}}

## Choosing Between REST, gRPC and GraphQL

All three are just different answers to "how does a client talk to a server", and the right one depends on who the client is and what shape the problem has, not which is objectively "better".

**REST** is the default choice, and stays the default unless something specific pushes you away from it.

* The API is public, or consumed by parties you don't control. REST's use of plain HTTP/JSON means every language, tool and human can read it without special tooling.
* The resource model is simple and maps cleanly to CRUD (`GET /posts/{id}`, `POST /posts`). You don't need clients able to shape their own responses.
* You want to lean on standard HTTP infrastructure as-is: caching (`ETag`, `Cache-Control`), CDNs, browser support, load balancers, API gateways, all of it already understands REST.
* The team is small or the org is polyglot and you'd rather not ask every consumer to learn a schema language or codegen step.

**gRPC** earns its complexity when performance and strict contracts between services you control matter more than human-readability.

* Service-to-service calls inside your own infrastructure, not public-facing APIs, since gRPC needs HTTP/2 and Protobuf tooling on both ends.
* Latency and payload size actually matter: Protobuf's binary format and HTTP/2 multiplexing beat JSON/REST on the wire, which adds up at high request volumes.
* You need streaming, gRPC has first-class support for client, server and bidirectional streaming, which REST has to bolt on with things like SSE or long polling.
* You want the contract enforced at compile time, a `.proto` file generates strongly typed client and server stubs, so a field type or method signature mismatch fails the build, not production.

**GraphQL** is worth the extra server-side complexity when the *client's* needs are the variable, not the server's.

* Multiple, different clients (web, mobile, third-party integrations) each want a different shape or subset of the same underlying data, and you'd otherwise end up building bespoke REST endpoints per client, or heavily over-fetching.
* A single logical request would otherwise mean multiple REST round trips (get a post, then its comments, then each comment's author), and the client would rather ask for that whole tree in one request.
* The data comes from several disparate sources (services, databases) and you want to expose it behind one coherent, browsable schema rather than making the client stitch together several APIs.
* You value the schema itself as living documentation, introspection means the API is self-describing and tools like GraphiQL give you a working playground for free.

**Falcor and OData** solve the same over/under-fetching problem as GraphQL, without introducing a new query language.

* Falcor (built at Netflix) models the entire backend as one virtual JSON object graph the client can path into, so the client's query is just a JSON path expression instead of a GraphQL document. It's largely dormant today, most teams that would have reached for it now reach for GraphQL instead, but it's worth knowing it exists if you ever see it in an older codebase.
* OData is a REST-based (mostly Microsoft/enterprise-ecosystem) standard that adds filtering, sorting, pagination and field-selection conventions on top of plain REST URLs (`GET /Posts?$filter=...&$select=header,createdBy`), rather than a separate query language and endpoint. It's worth considering over GraphQL when the team wants REST's caching and tooling story but with more flexible querying than hand-rolled query parameters.

The projects don't have to be mutually exclusive within the same system. It's common to expose GraphQL or REST at the edge for clients, while services behind that edge talk to each other over gRPC, using each protocol where it's actually the better fit rather than picking one for the entire stack.

## References

[https://spring.io/projects/spring-graphql](https://spring.io/projects/spring-graphql)

[https://github.com/graphql-java/graphql-java-extended-scalars](https://github.com/graphql-java/graphql-java-extended-scalars)

[https://www.graphql-java.com/tutorials/getting-started-with-spring-boot/](https://www.graphql-java.com/tutorials/getting-started-with-spring-boot/)

[https://spring.io/blog/2022/05/19/spring-for-graphql-1-0-release](https://spring.io/blog/2022/05/19/spring-for-graphql-1-0-release)

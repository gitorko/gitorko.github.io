---
title: 'Spring AI - Ollama with AI Agent'
description: 'Spring AI - Ollama'
summary: 'Spring AI - Ollama'
date: '2024-12-22'
aliases: [/alias/]
author: 'Arjun Surendra'
categories: [Spring, AI, Ollama, LLM, Agent]
tags: [ollama, ai, llm, agent, postgres]
toc: true
draft: false
---

Spring AI - Ollama (Chat Model)

Github: [https://github.com/gitorko/project09](https://github.com/gitorko/project09)

## Spring AI

Ollama is a platform designed to allow developers to run large language models (LLMs) locally.

In this example we will run the **llama3.1** LLM model which will run locally and write an AI agent that can interact with the postgres database to create a TODO task application.

### Code

{{< ghcode "https://raw.githubusercontent.com/gitorko/project09/refs/heads/main/src/main/java/com/demo/project09/controller/HomeController.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project09/refs/heads/main/src/main/java/com/demo/project09/config/ChatConfig.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project09/refs/heads/main/src/main/java/com/demo/project09/agent/ChatAgent.java" >}}

{{< ghcode "https://raw.githubusercontent.com/gitorko/project09/refs/heads/main/src/main/java/com/demo/project09/agent/TodoAgent.java" >}}

### Postman

Import the postman collection to postman

[Postman Collection](https://raw.githubusercontent.com/gitorko/project09/main/postman/Project09.postman_collection.json)

![](img01.png)

![](img02.png)

![](img03.png)

![](img04.png)

![](img05.png)

![](img06.png)

![](img07.png)

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project09/main/README.md" >}}

## References

[https://spring.io/projects/spring-ai/](https://spring.io/projects/spring-ai/)

[https://docs.spring.io/spring-ai/reference/api/chat/ollama-chat.html](https://docs.spring.io/spring-ai/reference/api/chat/ollama-chat.html)

[https://hub.docker.com/r/ollama/ollama](https://hub.docker.com/r/ollama/ollama)

[https://ollama.com/](https://ollama.com/)
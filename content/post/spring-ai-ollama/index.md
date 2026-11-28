---
title: 'Spring AI - Ollama with AI Agent and RAG'
description: 'Spring AI - Ollama'
summary: 'Spring AI - Ollama agent with tool calling, memory and PDF Q&A (RAG)'
date: '2024-12-22'
aliases: [/alias/]
author: 'Arjun Surendra'
categories: [Spring, AI, Ollama, LLM, Agent, RAG]
tags: [ollama, ai, llm, agent, postgres, rag, pgvector]
toc: true
draft: false
---

Spring AI - Ollama (Chat Model, Tool Calling, Memory and RAG)

Github: [https://github.com/gitorko/project09](https://github.com/gitorko/project09)

## Spring AI

Ollama is a platform designed to allow developers to run large language models (LLMs) locally, no API key or cloud needed.

In this example we run the **llama3.1** chat model and the **mxbai-embed-large** embedding model locally, and build one Spring Boot app with two features:

1. **Todo assistant & chat** - an AI agent that adds/gets/searches/deletes todo rows in Postgres using tool calling. It is restricted to todo requests by a scope guard, keeps conversation memory, and remembers long-term facts per user. `/talk` is plain, unrestricted chat.
2. **PDF Q&A (RAG)** - upload PDFs into sections (stored in the DB), then ask a section a question. Answers come only from that section's PDFs, with sources (document and page).

```text
Todo:  message --> scope guard --> LLM + tools (todo rows, memory) --> reply

PDFs --> Postgres (bytes) --> PDF reader --> chunker --> Ollama embeddings --> pgvector (tagged with section)
(section, question) --> embed --> similarity search in section --> (gate 1: threshold) --> Ollama chat (gate 2: prompt) --> answer + sources
```

### Why a vector database?

A normal database finds rows by exact values or keywords. That fails for natural questions: *"How many days off do I get?"* shares no keyword with *"Employees are entitled to 25 days of annual leave"*.

An embedding model turns text into a vector so that texts with similar meaning are close together. A vector database (pgvector with an HNSW index here) quickly finds the nearest vectors. Retrieval gives the LLM only the few relevant chunks, since its context window is small and it has never seen your private data. This is RAG (retrieval-augmented generation): answers are grounded in your content, can cite sources, and new knowledge is added by inserting documents, with no retraining.

### Features and the AI concepts they show

**1. Todo assistant (`POST /agent`) - tool calling (function calling)**

You type a natural-language message such as *"add buy milk"*, *"what is task 2?"* or *"delete task 3"*. The LLM does not touch the database itself. Instead the app exposes Java methods as **tools** (add, get, search, delete a todo row in Postgres) and the model decides which tool to call and with what arguments. Spring AI executes the tool, hands the result back to the model, and the model writes the final reply. This is what turns a chat model into an *agent* that can act on real data.

**2. Scope guard - guardrails**

The agent is restricted to todo requests only. A guard checks each message first, so off-topic requests (*"write me a poem"*) and prompt-injection attempts (*"ignore your instructions and..."*) are refused before they reach the tools.

**3. Conversation memory (`/agent/history`) - Spring AI Session API, advisors and compaction**

LLMs are stateless, so previous messages must be sent back with every call. Here a Spring AI **advisor** (`SessionAdvisors`) wraps the `ChatClient` and stores each conversation in Postgres through `SessionService`, so follow-ups like *"delete it"* work and history survives restarts. When a conversation grows past a number of turns, **compaction** kicks in: older turns are summarized by the model in the background and only the last few turns are kept verbatim, so the 4096-token context window is not exhausted. History can be shown or reset.

**4. Long-term user memory (`/agent/memories`) - persistent memory as tools**

The model can save durable facts about a user (*"remember I am vegetarian"*), recall them in later conversations, and forget them on request. Facts are kept in the database per user, separate from short-term conversation history, and injected into the system prompt. The user id is passed to the tools through Spring AI's `ToolContext` (set by the server from the request) so the model can never choose or spoof whose memory it touches.

**5. Plain chat (`POST /talk`, `GET /time`)**

`/talk` is unrestricted chat with the local llama3.1 model with no tools. `/time` shows a simple tool the model can call to get the current time.

**6. PDF Q&A (`/api/sections`, `/api/documents`, `POST /api/ask`, `GET /api/search`) - RAG**

Upload PDFs into named sections (the files are stored in Postgres). Each PDF is read, split into chunks, converted to embeddings by **mxbai-embed-large**, and stored in **pgvector** tagged with its section. When you ask a section a question, the question is embedded, the most similar chunks in that section are found by similarity search, and only those chunks are given to llama3.1 as context. This is **RAG (retrieval-augmented generation)**. Retrieval uses a vector-store **metadata filter** (`FilterExpressionBuilder`) on section, and optionally on selected documents, so one section's answers never leak from another's. Two gates keep answers honest: a similarity threshold (no relevant chunk means no answer is attempted) and a prompt that tells the model to answer only from the supplied text. The response includes the **sources** (document and page).

**7. Configuration and resilience**

- `temperature: 0.0` keeps tool calling and RAG answers deterministic, and `num-ctx: 4096` sets the context window.
- Ollama models are pulled automatically at startup if missing (`pull-model-strategy: when_missing`).
- Spring AI retries are reduced to 2 attempts so the API fails fast, and `TransientAiException` / `NonTransientAiException` are mapped to HTTP 503 when Ollama is unavailable, instead of hanging for minutes.

### Setup

{{< ghcode "https://raw.githubusercontent.com/gitorko/project09/main/README.md" >}}

## References

[https://spring.io/projects/spring-ai/](https://spring.io/projects/spring-ai/)

[https://docs.spring.io/spring-ai/reference/api/chat/ollama-chat.html](https://docs.spring.io/spring-ai/reference/api/chat/ollama-chat.html)

[https://docs.spring.io/spring-ai/reference/api/vectordbs/pgvector.html](https://docs.spring.io/spring-ai/reference/api/vectordbs/pgvector.html)

[https://hub.docker.com/r/ollama/ollama](https://hub.docker.com/r/ollama/ollama)

[https://ollama.com/](https://ollama.com/)

## Other areas to explore

Note: the topics below are related AI areas worth covering, but they are **not part of this project** currently.

- **Structured output** - map the model's reply to a Java record/DTO instead of free text.
- **Streaming responses** - return tokens as they are generated (SSE / `Flux`).
- **Hybrid search and re-ranking** - combine vector similarity with keyword/full-text search.
- **Chunking strategy and richer metadata filters** - tune chunk size and overlap, and filter by more than section and document (date, author).
- **RAG evaluation** - measure relevancy and groundedness of answers (evaluators, LLM-as-judge).
- **Observability** - metrics and tracing for token usage, latency and tool calls.
- **Indirect prompt injection** - malicious instructions hidden inside uploaded PDFs.
- **MCP (Model Context Protocol)** - expose or consume tools through MCP servers.
- **Advisors for RAG and guardrails** - advisors are used here only for session memory; RAG and the scope guard are written as plain code, and could be composed as advisors (for example `QuestionAnswerAdvisor`).
- **Multimodal and OCR** - image input with vision models, scanned PDFs.
- **Multi-agent / routing** - one router choosing between the todo agent and RAG.
- **Local model limitations** - tool-calling mistakes on small models and the small context window.

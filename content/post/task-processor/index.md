---
title: 'A Fair, Adaptive Task Processor for Java'
description: 'A Java task processing library with priority, fair scheduling, adaptive polling, retries, and runtime statistics.'
summary: 'A look at a Java task processor designed to accept, queue, and execute short or long running work across machines with different capacities.'
date: '2026-11-24'
aliases: []
author: 'Arjun Surendra'
categories: [Java, Backend]
tags: [task-processing, java, spring-boot, queues, concurrency]
toc: true
draft: false
---

A task processor should make it straightforward to accept work, queue it safely, and execute it efficiently across machines with different CPU and I/O capacity. This library provides a backend API for submitting and managing tasks, with priority-aware scheduling, fairness, adaptive polling, retries, and operational statistics.

Github: [https://github.com/gitorko/project18](https://github.com/gitorko/project18)

## What it provides

The library exposes a REST API for submitting tasks and inspecting their state. Tasks are persisted in PostgreSQL, so accepted work survives process restarts. Workers claim work from the database and execute it asynchronously.

The design targets both short and long running tasks. Worker concurrency and polling settings can be tuned for the hardware and workload, while task state and aggregate metrics help operators understand what is happening.

## Priority with fairness

Priority helps urgent tasks move ahead of ordinary work. A strict priority queue, however, can leave low-priority tasks waiting indefinitely when high-priority traffic is constant.

The processor combines priority ordering with fairness controls so that lower-priority work continues to make progress. This gives callers a way to express importance without making the queue permanently unfair.

## Adaptive polling

Workers poll for available tasks rather than continuously spinning. The polling interval adapts to queue activity: workers can check more frequently when work is available and back off when the queue is idle. This reduces unnecessary database traffic while keeping task pickup responsive under load.

## Running on different hardware

A worker pool that is effective on a large server can overwhelm a small instance. The processor allows concurrency and polling to be configured to match available resources and the task mix.

CPU-bound work generally benefits from concurrency close to the number of available cores. I/O-bound work can use more concurrent workers because tasks spend time waiting. Operators can tune the worker capacity and observe queue depth, active work, and execution duration to find a suitable setting for each deployment.

## Retry on failure

Transient failures should not always mean lost work. The processor supports retrying failed tasks, with attempts tracked so that work can be retried according to the configured policy. Retry behavior is useful for temporary downstream errors and other recoverable failures.

Retries should be configured with care: tasks with external side effects should be idempotent, or otherwise guard against the same operation being performed more than once.

## Task and queue statistics

The API provides operational information about task execution and queue state, including:

- How many tasks are running and waiting.
- Task counts grouped by type and status.
- Average execution time for completed tasks.
- Queue backlog and an estimated time to completion based on observed processing rates.

These statistics give applications and operators a view into throughput and backlog without requiring a separate UI.

## How it compares with JobRunr

JobRunr is a mature Java background-job framework with persistent storage, background servers, retries, and a dashboard. This library focuses on a smaller backend task-processing API and does not include a UI. It emphasizes priority and fair queue behavior, adaptive polling, and queue/runtime statistics.

| Capability | This task processor | JobRunr OSS |
| --- | --- | --- |
| Java background execution | Yes | Yes |
| Persistent storage | PostgreSQL | Multiple supported storage providers |
| Retry on failure | Supported | Supported, with automatic retries and backoff |
| Priority and fairness | Priority-aware scheduling with fairness controls | Priority queues are a Pro feature |
| Adaptive polling | Adaptive worker polling | Background server configuration and worker management |
| Queue and runtime statistics | Task counts, averages, backlog ETA | Metrics and job/server monitoring |
| Built-in UI | No | Dashboard available |
| Scheduled jobs | Not a focus | Supported |

JobRunr offers a broader feature set, including scheduling and a dashboard, as well as a choice of persistence backends. Its OSS edition is a strong option when those capabilities are important. This library is aimed at applications that want an embeddable backend task API with explicit control over queueing, fairness, and worker behavior.

## Current scope

This library is focused on accepting, queuing, and executing tasks. Scheduled job promotion is not currently part of the implementation, and runtime statistics and backlog estimates are based on the processor's observed state and execution history. Deployments should tune worker capacity for their task types and hardware.

## Setup

See the project README for build and configuration instructions:

{{< ghcode "https://raw.githubusercontent.com/gitorko/project18/main/README.md" >}}

## References

- [JobRunr documentation](https://www.jobrunr.io/en/documentation/)
- [JobRunr retries](https://www.jobrunr.io/en/documentation/background-methods/dealing-with-exceptions/)
- [JobRunr metrics](https://www.jobrunr.io/en/documentation/configuration/metrics/)

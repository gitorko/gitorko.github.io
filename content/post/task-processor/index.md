---
title: 'A Fair, Adaptive Task Processor for Java'
description: 'A beginner-friendly look at a Java task processor with distributed workers, priorities, retries, adaptive polling, and metrics.'
summary: 'How a database-backed task queue accepts work, shares it across workers, and reports on its progress.'
date: '2026-11-24'
aliases: []
author: 'Arjun Surendra'
categories: [Java, Backend]
tags: [task-processing, java, spring-boot, queues, concurrency]
toc: true
draft: false
---

A web request does not always need to do all of its work before replying. Sending an email, generating a report, or resizing an image may take seconds or minutes. Making the caller wait for all of that work can tie up a web request and make the application feel slow.

A task processor gives the application another option: accept a description of the work, put it in a queue, and let workers carry it out in the background. This Java library provides that flow through a REST API, with PostgreSQL-backed storage, distributed workers, named priorities, retries, adaptive polling, and metrics.

Github: [https://github.com/gitorko/project18](https://github.com/gitorko/project18)

## The basic idea

A **task** is a saved description of work, such as “send this email,” together with the data needed to do it. A **queue** is the waiting line for tasks. A **worker** is an application instance that takes a task from the line and runs the code that handles it.

A client submits a task to the API. The API saves it in PostgreSQL and returns the task details. A worker later claims the saved task, calls the registered handler for its task type, and records whether it completed or failed. Because the task is stored before the API responds, it does not disappear just because the application restarts.

This separates accepting work from doing work: the client can continue while the processor handles the task in the background.

**Why durable background processing matters:** Slow work inside a web request
makes the caller wait and ties up a server thread. Saving the task first lets
the API respond while the work continues independently.

**How it behaves:** The API stores tasks in PostgreSQL. Workers claim saved
tasks, run the handler registered for the task type, and record the result.
Accepted work remains in the database if the application restarts.

**Without it:** The request must wait for the work to finish, or the
application must build its own queue and worker system. In-memory-only work
can disappear when a process restarts.

## Routing tasks to handlers

The queue stores a task type and a text payload. A **handler** is the
application code that knows what to do with that type, such as sending an
email or generating a report.

**Why it matters:** A generic queue can store many kinds of work, but it needs
an explicit way to connect each stored task to the application code that
performs it.

**How it behaves:** Register a Spring bean that implements `TaskHandler` and
returns the task type from `getTaskType()`. The processor routes matching
tasks to that handler and passes it the saved task and payload. The built-in
`DefaultTaskHandler` is a sample fallback: if no handler matches, it logs the
task and marks it complete. Production applications should register a real
handler for every task type or replace that fallback.

**Without it:** The processor has no application-specific action to run. A
missing handler can be especially risky with the sample fallback, because the
task may be marked complete even though its intended work did not happen.

## Idempotent task submission

“Idempotent” means repeating the same request has the same effect as sending it
once. It matters when a client times out and cannot tell whether the server
accepted its task.

**Why it matters:** Without a safe way to retry the HTTP request, the client
must choose between risking a lost task and risking a duplicate task.

**How it behaves:** A client sends `Idempotency-Key` with
`POST /api/v1/tasks`. The same key and request returns the original task. Using
that key with different request data returns `409 Conflict`. This applies to
single submissions, not bulk submissions.

**Without it:** Retrying after a lost response can create duplicate tasks.
This key only deduplicates acceptance; a handler can still run more than once
after worker failure. Handlers and external side effects must tolerate that.

## Submitting a batch of tasks

When a client has many independent tasks, it can submit them together through
`POST /api/v1/tasks/bulk` instead of making one HTTP request per task.

**Why it matters:** Sending a large set one request at a time adds network
round trips and makes it harder to know whether the whole batch was accepted.

**How it behaves:** The API accepts up to 1,000 tasks in one request. It
validates and saves the batch together. If a queue admission limit would be
exceeded, the whole batch is rejected; bulk submission does not support
`Idempotency-Key` yet.

**Without it:** Clients must send tasks individually, or build their own batch
endpoint. Retrying an uncertain bulk request can create duplicates because
there is no bulk idempotency key.

## Run a task later or repeat it on an interval

Sometimes work should wait until a particular time. A one-time task can include
`scheduledAt`, an ISO-8601 date and time, when it is submitted to
`POST /api/v1/tasks`. For example, `2026-11-25T09:00:00Z` means “do not run this
before 9:00 UTC on that date.” A small dispatcher checks for tasks whose time
has arrived and moves them into the normal queue.

To repeat work, create a schedule at `POST /api/v1/schedules` with an
`intervalSeconds` value. An interval schedule stores the task instructions once
and creates a new task record for each run. Each occurrence has its own task ID,
so its result and failure can be inspected independently.

The interval is measured from when the previous run finishes. For example, an
interval of 60 seconds means the next run becomes due 60 seconds after the last
run completes. This fixed-delay rule prevents a slow task from overlapping with
itself and avoids a flood of catch-up runs after the service has been offline.
The response gives the schedule ID and next run time; clients can inspect or
cancel the schedule through the schedule API.

```json
{
  "taskType": "refresh-cache",
  "priority": "MEDIUM",
  "scheduledAt": "2026-11-25T09:00:00Z",
  "intervalSeconds": 3600,
  "maxAttempts": 3
}
```

This creates a recurring task that first runs at the specified time and then
waits one hour after each finished run before creating the next occurrence.

**Why it matters:** Some work should run later or repeat without a client
submitting every occurrence, such as refreshing a cache each hour.

**How it behaves:** A one-time task uses `scheduledAt`. A recurring schedule
uses `intervalSeconds`; each occurrence gets its own task record. The interval
starts after the previous run finishes, preventing overlap and catch-up bursts
after downtime. These schedules use fixed delays, not cron expressions.

**Without it:** Clients must track run times and submit each task themselves.
A client outage could miss an occurrence or submit several at once after it
recovers.

## Workers across multiple machines

A worker is not a special server that needs to be elected as the leader. You can run several copies of the application, and they can all look for tasks in the same PostgreSQL queue. This is useful when one machine cannot process the incoming work quickly enough, or when you want to use a mix of smaller and larger machines.

When a worker takes a task, PostgreSQL marks the task row as locked for the short claim transaction. The query uses `SKIP LOCKED`: if another worker already locked a row, this worker skips it and looks for a different task. That prevents two workers from claiming the same task at the same time without making them wait in line for each other.

The lock is released after the worker marks its task as processing. It does not stay held while the handler runs. Worker heartbeats record that processing is still active, and stale-task recovery can return tasks left behind by a worker that stopped unexpectedly.

**Why it matters:** One machine may not have enough capacity for incoming
work. Multiple workers can increase throughput and let the system use a mix of
hardware sizes.

**How it behaves:** Worker instances share PostgreSQL. A short
`SELECT ... FOR UPDATE SKIP LOCKED` claim lets each worker take different rows
without waiting on rows already claimed. Heartbeats help detect work abandoned
by a worker that stopped.

**Without it:** A single worker limits throughput and is a single point of
failure. Without coordinated claims, workers could run the same task at once.
Recovery can still repeat a task, so handlers must tolerate duplicate
execution.

## Worker timeouts and crash recovery

A worker can become stuck or stop while it owns a task. A **heartbeat** is a
small periodic update that tells the database a worker is still handling its
task.

**Why it matters:** Without a way to detect abandoned work, tasks claimed by a
crashed machine can remain `PROCESSING` forever and never return to the queue.

**How it behaves:** Workers update all of their active tasks every five
seconds by default, using a single batched database update rather than one
update per task. If no heartbeat arrives for 30 seconds, recovery treats the
task as stale: it returns it to `PENDING` when attempts remain, or marks it
`FAILED` when the attempt limit has been reached. A task can also specify
`timeoutSeconds`; the default timeout is 120 seconds. A separate watchdog thread
checks for overrunning tasks every second, so timeouts are enforced promptly
even if heartbeat writes are slow. Timeout cancellation requests Java thread
interruption, which handlers must honor.

When a worker is shut down gracefully, it first waits for running tasks to
finish (30 seconds by default). Tasks still running after that are handed
straight back to the queue, and only then are their threads interrupted, so the
interruption cannot make a well-behaved handler report a failure that burns an
attempt. Nothing has to wait for the stale threshold, and that aborted attempt
is not counted against `maxAttempts`.

**Without it:** A worker crash can leave claimed tasks stuck indefinitely.
If a handler ignores interruption, timeout handling cannot forcibly stop its
Java code, so the thread may remain occupied even after the task is retried.

## Priority and fairness

Priority answers the question, “Which waiting task should be picked first?” The API uses named levels instead of numbers:

- `HIGH` for urgent work.
- `MEDIUM` for ordinary work, and the default when priority is omitted.
- `LOW` for work that can wait longer.

For example, a password reset email might be `HIGH`, while a nightly report might be `LOW`.

Within one priority level, the processor also shares a batch across task types in round-robin order. If one type has a large backlog, it should not take every available slot while another type at the same priority waits. Higher priority still wins over lower priority, so a constant stream of urgent tasks can delay lower-priority work.

Example request:

```json
{
  "taskType": "send-email",
  "payload": "{\"to\":\"reader@example.com\"}",
  "priority": "HIGH",
  "maxAttempts": 3
}
```

**Why it matters:** Some tasks are more urgent than others, and a large backlog
of one task type should not take every available slot from other types.

**How it behaves:** Tasks use `HIGH`, `MEDIUM`, or `LOW` priority. Higher
priorities are selected first. Within each priority, the processor shares
work across task types in round-robin order.

Per-type concurrency caps (described below) interact with fairness too. When a
type has used all of its slots on a worker, the claim query leaves that type out
entirely, and if a type reaches its cap part-way through filling a batch, the
worker looks deeper in the queue. This keeps a capped type with a huge backlog
from filling the candidate window and starving everything behind it.

**Without it:** Urgent work may wait behind routine work, and a busy type may
crowd out other types. A continuous stream of high-priority work can still
delay lower priorities.

## Adaptive polling: checking the queue at the right pace

Workers need to check the database to find out whether new tasks are waiting. This repeated check is called **polling**. Checking very often makes new tasks start quickly, but it also creates extra database queries when the queue is empty. Checking only occasionally saves queries, but tasks may sit waiting until the next check.

**Adaptive polling** adjusts the wait between checks based on what the worker finds. When the queue is empty, this processor doubles the wait up to its configured maximum. When it finds tasks, it returns to the minimum wait so new work is picked up quickly. In the default configuration, the interval backs off up to five seconds and resets to 100 milliseconds when work appears.

A finished task also frees a worker slot. If the last check found work, the worker checks again right away (never faster than the minimum interval) instead of leaving that slot idle until the next scheduled check. When the queue is empty this shortcut is skipped, so quiet periods still back off.

This is a practical tradeoff: fewer unnecessary checks during quiet periods, and quicker pickup when work is flowing.

**Why it matters:** Polling very often creates database queries while idle;
polling too slowly leaves new work waiting between checks.

**How it behaves:** When the queue is empty, the worker lengthens the wait
between checks up to a configured maximum. When it finds tasks, it returns
toward the minimum interval, and a finished task triggers an early check while
there is work waiting. Defaults range from 100 milliseconds to five seconds.

**Without it:** A fixed short interval wastes database capacity during idle
periods. A fixed long interval saves queries but adds pickup delay when tasks
arrive.

## Matching worker capacity to hardware

Each application instance has a pool of threads that run task handlers. A thread is like a worker at a desk: while it is doing one task, it cannot start another. The pool size controls how many tasks that instance can handle at once.

CPU-heavy tasks, such as image conversion, keep the processor busy and usually need a smaller pool. Tasks that mostly wait for another service, such as an HTTP call, can often use more threads because those threads are waiting instead of using CPU. The right setting depends on the task and the machine, so the pool size and polling settings can be adjusted for each deployment.

The poller also checks available local worker capacity before claiming more tasks. That helps avoid claiming a large batch that would simply wait in the instance's in-memory executor queue. If a claimed task cannot be handed to the thread pool, for example because the pool is shutting down, it is released back to the queue rather than being run on the polling thread or left marked as processing.

Different task types can have different concurrency caps on each worker. For
example, a deployment can allow only two report-generation tasks at once, so
long CPU-heavy reports do not occupy every thread while short email tasks wait.
These are per-instance limits; each machine applies its own configured caps. If
three workers each allow two reports, the deployment can run up to six report
tasks at once. The limit shares the regular thread pool; it does not reserve
dedicated threads, and task types without a cap can use any free slot.

For instance, a worker with an eight-thread pool could set `REPORT: 2` and
`EMAIL: 6` under `task-processor.poller.max-concurrent-by-type`. Choose caps
based on each task type's resource use and the machine's CPU and memory.

**Why it matters:** CPU-heavy or memory-heavy work can consume resources
needed by other tasks. Tasks that mostly wait for a network response may be
able to run with more concurrency.

**How it behaves:** Pool size sets the overall thread capacity. Per-type caps
limit selected task types on each worker; uncapped types can use available
slots. The poller checks capacity before claiming work. Caps apply per
instance, so three workers each allowing two reports can run six reports in
total.

**Without it:** One resource-heavy type can occupy all threads. A pool that is
too small wastes available capacity; one that is too large can oversubscribe
CPU or memory. Tune pool size and caps for the hardware and workload.

## Runtime worker controls

Capacity needs can change after an application starts, for example when a
traffic spike begins or a machine is under pressure. The poller API exposes
runtime controls for a worker.

**Why it matters:** Without runtime controls, an operator who needs to change
processing capacity must edit configuration and restart the service.

**How it behaves:** The API can report worker status and update pool size,
batch size, polling interval, and enabled state. It can also start or stop
polling. These controls affect only the worker receiving the request.
Stopping pauses new polling and waits for active tasks to drain; the same
worker can be started again afterward. Application shutdown closes the
executor.

**Without it:** Capacity settings can only be changed through deployment
configuration and a restart, which can take longer during a load spike.

## Cancelling queued work

Tasks that are no longer needed can be cancelled with
`DELETE /api/v1/tasks/{taskId}`. A recurring schedule can be stopped with
`DELETE /api/v1/schedules/{scheduleId}`.

**Why it matters:** Users may withdraw a request, or a queued task may become
irrelevant before a worker starts it.

**How it behaves:** A pending or delayed task can be marked `CANCELLED`. A
schedule cancellation prevents future occurrences and cancels its queued
occurrence. A handler already running is allowed to finish; the task API does
not interrupt active handler code.

**Without it:** Unneeded queued work still consumes capacity and may perform
actions after they are no longer relevant. Running work still needs
cooperative cancellation inside the handler.

## Retry after failure

A task can fail because a downstream service is temporarily unavailable or because of another error in its handler. The API lets the caller set `maxAttempts`. If attempts remain, the task waits before trying again. The delay grows exponentially: one second, two seconds, four seconds, and so on, up to a one-minute default cap. A small random amount called **jitter** is added so a group of failed tasks does not all retry at exactly the same time. When attempts run out, the task becomes `FAILED` and stays available for inspection until the retention purge removes it (see below).

This is called **at-least-once execution**: a task will be retried after failures, but it may run more than once. For example, a worker could charge a card and then crash before saving the success status. A retry could attempt the charge again. Handlers should be safe to repeat or use an idempotency key with the downstream service. The API's `Idempotency-Key` only prevents duplicate task acceptance; it cannot guarantee exactly-once side effects inside a handler.

Failed tasks act as dead-letter records: they remain in the database until the retention purge removes them, and can be listed with `GET /api/v1/tasks?status=FAILED`. `POST /api/v1/tasks/{taskId}/replay` creates a new task linked to the failed record. This lets an operator correct the underlying issue and retry the work without erasing the original failure.

`maxAttempts` includes the first run. Its default is one, so retries must be
enabled by setting a larger value. The initial delay, maximum delay, and jitter
factor can be changed with `task-processor.retry.initial-delay-ms`,
`task-processor.retry.max-delay-ms`, and `task-processor.retry.jitter-factor`.

**Why it matters:** Temporary failures may recover if work is tried again.
Immediate retries can overload an unhealthy service, and failed work needs to
remain visible for diagnosis.

**How it behaves:** `maxAttempts` includes the first run and defaults to one.
When attempts remain, the delay grows exponentially up to a cap, with random
jitter to spread out retries. Exhausted tasks remain `FAILED` until purged; replay creates
a new linked task and preserves the original failure. Polling selects only
`PENDING` tasks, so a `FAILED` row is never picked up by the next poll. A
failed task is retried only if someone explicitly replays it, which creates a
new task record.

**Without it:** Temporary errors can permanently lose work. Immediate retries
can hammer a failing dependency, while discarding failures removes the record
needed to investigate and replay them.

## Cleaning up finished tasks

A queue that keeps every finished task grows forever. The table, its indexes,
and the summary queries all get slower as old `COMPLETED`, `FAILED`, and
`CANCELLED` rows pile up.

**Why it matters:** Finished tasks are only useful for a while: to confirm a
result, diagnose a failure, or replay it. After that they only cost storage and
query time.

**How it behaves:** A background job deletes finished tasks that were last
updated before a retention window, in small batches so it never holds long
locks. It also removes the idempotency record that points at each deleted task,
and it never deletes a task that a recurring schedule is still tracking. The
window is `task-processor.retention.retention-hours`. The default is `0`, which
deletes finished tasks on the next purge run (every five minutes by default);
set a larger value such as `168` to keep them for a week, or disable the purge
with `task-processor.retention.enabled: false`.

**Without it:** The task table grows without bound. With the default of `0`,
note the trade-off: a finished task is quickly gone, so it can no longer be
fetched by ID, a failed task can no longer be replayed after the purge, and a
retry with the same idempotency key creates a new task. Raise the retention
window if you rely on any of those.

## Metrics and queue statistics

Metrics are measurements that help you see how the processor is behaving: how many tasks were accepted, how many completed or failed, how long processing takes, and how busy the worker pool is. The library records these measurements with **Micrometer**, a Java metrics toolkit, and includes a **Prometheus** endpoint. Prometheus can periodically read that endpoint and store the measurements for dashboards or alerts.

The task summary API also reports database-backed counts (gathered with a single grouped query rather than one count per status) for waiting, running, and finished tasks (finished counts stay small when the retention purge is on); counts grouped by task type; average processing time; and an estimated time to drain the current backlog.

**Why it matters:** Measurements help operators see if the queue is growing,
which task types are slow, and whether worker capacity is being used.

**How it behaves:** Micrometer metrics are exposed through Prometheus. The
summary API provides task counts, per-type counts and average runtime, worker
pool information, and an estimated backlog drain time. Counts are stored in
the database, but runtime measurements are process-local, so multi-worker
estimates are approximate. ETA is not a completion guarantee.

Beyond totals, the processor records how long each task **waited** between
becoming runnable and being claimed (the clearest sign that workers are falling
behind), how long each **attempt** ran, labelled by outcome (completed, retried,
failed, or timed out), and counters for retries, timeouts, tasks recovered by
stale-task recovery, tasks released at shutdown, and tasks purged.

To follow one task through the logs, every request carries a **correlation id**:
the caller's `X-Correlation-Id` header, or a generated one. It is stored on the
task and added to the logging context, together with the task id and attempt
number, while the task runs. Searching the logs for that id shows both the
request that enqueued the task and the worker that ran it.

**Without it:** A growing queue or repeated failures may only become visible
through user complaints or manual database inspection.

## Backpressure: when should a client slow down?

**Backpressure** is a way for a busy system to signal that work is arriving faster than it can be completed. The client can check `GET /api/v1/tasks/summary` and look at `pendingTasks`, the per-type pending counts, and the estimated backlog completion time. If those values pass a limit chosen by the application, the client can pause briefly or send fewer tasks per second. Using one threshold to slow down and a lower threshold to resume helps avoid rapidly toggling between the two states.

The processor also enforces a global outstanding-task limit of 10,000 by default, with optional limits for individual task types. Outstanding includes waiting, running, and scheduled task rows. If a request would exceed a limit, the API returns `503 Service Unavailable` with `Retry-After: 5`; a bulk submission is rejected as a whole. The summary endpoint is still useful as an early warning, but it is only a point-in-time snapshot. Queue counts come from PostgreSQL, while throughput and ETA measurements are local to each worker process, so multi-worker estimates should be treated as approximate.

Queue counts come from PostgreSQL, while runtime rate and average-duration measurements are held in each process. In a multi-worker deployment, those runtime measurements are therefore per instance rather than one combined cluster-wide measurement.

**Why it matters:** If clients submit faster than workers finish tasks, the
queue can grow until it consumes too much database space or creates very long
waits.

**How it behaves:** Clients can use summary statistics as an early signal to
reduce submissions. The API also enforces a default global limit of 10,000
outstanding tasks, with optional per-type limits. Requests that exceed a limit
receive `503 Service Unavailable` with `Retry-After: 5`; bulk requests are
rejected as a whole. Outstanding includes pending, processing, and scheduled
task rows.

**Without it:** Queue growth is unbounded as long as clients can continue
submitting. Clients get no clear signal to pause, and storage use and task
wait times can keep increasing.

## How it compares with JobRunr

JobRunr is an established Java framework for background jobs. Both projects can persist work and run it on background workers. JobRunr also includes a dashboard and cron-based schedules. This library has no UI and supports one-time timestamps plus fixed-delay intervals.

| Capability | This task processor | JobRunr OSS |
| --- | --- | --- |
| Java background execution | Yes | Yes |
| Persistent storage | PostgreSQL | Multiple supported storage providers |
| Distributed workers | Multiple instances claim tasks from the shared PostgreSQL queue | Multiple background servers supported |
| Retry on failure | Supported | Supported, with automatic retries and backoff |
| Idempotent submission | Optional key for single-task requests | Supported |
| Failed task replay | New task linked to the failed record while it is retained | Failed jobs can be retried |
| Priority and fairness | Named priorities; round-robin by type within each priority | Priority queues are a Pro feature |
| Queue admission limits | Global and per-task-type outstanding limits | Configuration and plan dependent |
| Per-type worker caps | Configurable per worker instance | Worker configuration available |
| Adaptive polling | Poll interval backs off when the queue is empty | Background server configuration and worker management |
| Metrics and queue statistics | Micrometer/Prometheus metrics, task counts, averages, backlog ETA | Metrics and job/server monitoring |
| Built-in UI | No | Dashboard available |
| Scheduling | One-time run at a timestamp and fixed-delay intervals | One-time and recurring schedules, including cron |

JobRunr has a broader set of features and storage choices. This processor is aimed at applications that want to submit tasks through a backend API, run workers against a shared PostgreSQL queue, and tune worker behavior for their workloads.

## Current scope

Backlog completion time is an estimate based on observed runtimes and the current process's worker capacity; it is not a promise, especially when task types have different runtimes or several worker instances are running.

The queue currently uses PostgreSQL only. Its cleanup is a simple age-based
purge of finished tasks, with no per-status or per-type retention rules. It has
no cron or calendar-based schedules, bulk idempotency keys, or authentication
and authorization. Running handlers cannot be forcibly
cancelled, and metrics used in ETA calculations are local to each worker.
Worker pool sizing and per-type caps are configured manually; there is no
automatic CPU-based scaling. Task payloads are generic text, so the library
does not enforce per-type schemas or payload versions.

## Setup

See the project README for build and configuration instructions:

{{< ghcode "https://raw.githubusercontent.com/gitorko/project18/main/README.md" >}}

# Messaging and Asynchronous Communication

**Domain:** 05 - Architecture Patterns and Styles
**Primary sources:** Ultimate Microservices with RabbitMQ (Morlion, 2024); Practical Microservices Architectural Patterns: Event-Based (Christudas, 2019)

## Overview
Asynchronous messaging lets services communicate by putting messages onto a broker rather than calling each other directly. A producer drops a message and moves on; a consumer reads it when ready. The broker provides the buffering, routing, and delivery guarantees that turn fire-and-forget into reliable communication. This decoupling is what makes messaging the backbone of scalable, resilient microservice and event-driven systems: services can be down, slow, added, or removed without the producer caring.

This page is the practical, broker-level companion to the conceptual pages on event-driven architecture and enterprise integration patterns. It focuses on AMQP/RabbitMQ mechanics - exchanges, queues, bindings, routing keys - and the operational concerns that decide whether a messaging system is reliable: delivery guarantees, acknowledgements, retries, dead-lettering, competing consumers, and high availability. RabbitMQ is used as the concrete reference because it implements the AMQP 0-9-1 model that most of these concepts come from.

## Core concepts

### The AMQP 0-9-1 model
RabbitMQ is built on AMQP 0-9-1 regardless of the wire protocol chosen. The model has three entities and one rulebook:
- **Exchange.** Where a publisher drops a message. The publisher does not know what happens next. Multiple publishers can share an exchange. Exchanges can be durable (survive a broker restart) and auto-delete (removed when no queues remain bound).
- **Queue.** An endpoint that consumers subscribe to. Messages are delivered FIFO. Consuming is destructive: a delivered message is removed (except with the RabbitMQ Stream protocol). Key queue options: **durable vs. transient** (survives restart or not), **auto-delete** (removed when the last consumer disconnects), **exclusive** (usable only by the consumer that declared it, deleted on disconnect).
- **Binding.** The rule connecting an exchange to a queue. A binding pairs an exchange with a **routing key** - a short text label (max 255 chars), conventionally a categorization like `order.created` or `order.refunded`.

Crucial nuance: a durable queue survives a restart, but its messages survive only if they were published as **persistent**. Durability is a property of both the queue and each message.

### Exchange types (routing behavior)
- **Direct exchange.** Delivers to queues whose binding routing key matches the message routing key exactly. Best for simple, basic filtering.
- **Fanout exchange.** Ignores routing keys; delivers to every bound queue. Best for broadcast to all/many consumers. Fastest, since there are no keys to evaluate.
- **Topic exchange.** Wildcard matching on dotted routing keys: `*` matches one word, `#` matches zero or more. `order.*` matches `order.created` and `order.refunded`; `order.#` also matches `order.created.failed`. Best when you need flexible or open-ended categorization. More work to evaluate than direct/fanout.
- **Headers exchange.** Matches on header key-value pairs instead of routing keys, with an `x-match` of `all` or `any`. Order-independent and good for rich, multi-attribute filtering (user id, region, event name), but cannot express positional wildcard patterns like `report.*.sales.*.failed`.
- **Default exchange.** A special direct exchange to which every queue is automatically bound by its own name as the routing key; convenient for simple point-to-point sends (used, e.g., for reply/callback queues).

### Delivery guarantees, acknowledgements, and publisher confirms
- **Consumer acknowledgements.** *Automatic ack* (fire-and-forget): the broker considers the message delivered immediately. *Manual ack*: the consumer sends a positive ack on success, or a negative ack (reject) on failure; RabbitMQ can also nack multiple messages at once.
- **Publisher Confirms.** Acks happen on the consumer side; to guarantee the publisher's message was accepted, RabbitMQ adds Publisher Confirms - the broker confirms the message reached all required queues (or nacks it). For persistent messages, the confirm is sent only after the message is written to disk, so confirm + persistent + durable queue gives end-to-end delivery guarantee.
- **Guaranteed delivery (store-and-forward).** The messaging system persists messages locally before acknowledging the send, and does not delete a message from one store until it is safely stored in the next, so a message always lives on disk on at least one node until the receiver acknowledges it. (Christudas)

## Frameworks & models

### Core messaging patterns (broker-mediated)
- **Competing Consumers (Work Queues / Task Queues).** Multiple consumers subscribe to one queue; the broker hands each message to exactly one of them, round-robin by default. Scales throughput by adding consumers. (Morlion, citing Hohpe 2003)
- **Publish/Subscribe (broadcast).** Messages are copied to multiple queues so every consumer gets its own copy. (Morlion)
- **Request/Reply (RPC over a broker).** The client creates and subscribes to a callback queue, sends the request specifying that queue in the `ReplyTo` property (often via the default exchange), and waits for the server's reply. Brings a broker into an otherwise synchronous interaction.
- **Conversations.** Multi-message exchanges that maintain context across several request/reply rounds.

### Topology refinements
- **Exchange-to-exchange bindings.** RabbitMQ extends AMQP so an exchange can bind to another exchange, adding routing steps before a queue. Useful to evolve topologies (e.g., insert a topic exchange behind a fanout so new queues get filtered subsets without re-evaluating all keys), and to let per-user exchanges persist while transient queues come and go with device connections.
- **Priority queues.** A queue declared with a max priority (0–255, but 0–5 recommended) delivers higher-priority messages first - effective only when messages actually build up in the queue.
- **Consumer priorities.** Override round-robin so higher-priority consumers receive messages until blocked, then lower-priority ones take over; equal-priority consumers still share round-robin.

### Reliability and failure handling
- **Dead-lettering.** When a message is rejected with "do not requeue," expires via TTL, or overflows a queue length limit, it can be moved to a dead-letter exchange (any of the four types). RabbitMQ adds headers recording the reason, time, and dead-letter count. Operators can then drop, inspect, or replay the messages - even auto-route them back to the consumer, while watching the count to avoid infinite loops. (Morlion)
- **Retry strategies.** For unavoidable direct calls, retry with **exponential backoff** (e.g., 1s, 3s, …) and optionally **jitter** (randomized intervals) to avoid synchronized retry storms from many clients. Retries cannot be infinite - at some point, handle the error (run saga compensations and notify the user, or take an alternative action). (Morlion)
- **Circuit Breaker.** After a failure, stop calling the failing service for a preconfigured period and fail fast, then probe again. Keeps callers responsive (only the first call eats the timeout) and protects an already-struggling service from pile-on load. (Morlion)
- **Quorum queues.** Replicated, consensus-based queues for high availability - the recommended modern choice for durable, mission-critical queues across a RabbitMQ cluster. (Morlion)

## Patterns
*(Also added to PATTERN-CATALOG.md.)*

- **Competing Consumers (Work Queue)** - Intent: distribute messages from one queue across many consumers so one processes each. When to use: scale out throughput / parallelize independent tasks. Trade-offs: easy horizontal scaling vs. no per-consumer ordering guarantees and need for idempotent handlers.
- **Publish/Subscribe (broker)** - Intent: deliver a copy of each message to every interested consumer via fanout/topic exchanges and per-consumer queues. When to use: events with multiple independent reactors. Trade-offs: clean decoupling vs. message duplication and more queues to manage.
- **Request/Reply over a broker** - Intent: get a response asynchronously using a callback queue and correlation. When to use: you need a reply but want broker decoupling/resilience instead of a direct synchronous call. Trade-offs: resilience and buffering vs. added latency and client-side wait logic.
- **Dead Letter Exchange** - Intent: capture undeliverable, rejected, expired, or overflow messages for triage or replay. When to use: any production queue. Trade-offs: prevents loss vs. monitoring/draining burden and loop risk.
- **Retry with backoff and jitter** - Intent: tolerate transient failures without hammering the target. When to use: direct calls or redelivery to flaky dependencies. Trade-offs: smooths transient faults vs. latency and the need for a final give-up path.
- **Circuit Breaker** - Intent: stop calling a failing dependency, fail fast, and probe for recovery. When to use: high-throughput callers of services that can fail. Trade-offs: protects callers and callee vs. tuning thresholds and handling open-circuit fallbacks.
- **Priority Queue / Consumer Priority** - Intent: deliver important messages or route to preferred consumers first. When to use: mixed-urgency workloads where messages or consumers differ in priority. Trade-offs: responsiveness for hot items vs. extra resources and no effect when the queue rarely backs up.
- **Quorum Queue** - Intent: replicated, consensus-backed queues for HA durability. When to use: mission-critical durable queues in a cluster. Trade-offs: stronger availability/durability vs. replication cost and latency.

## Principles & best practices
- Match exchange type to intent: fanout for broadcast, direct for exact filtering, topic for flexible wildcard categorization, headers for multi-attribute matching - and let the chosen type document the intent even when topic could technically do everything.
- For real delivery guarantees, combine three things: durable queue + persistent message + publisher confirm (and manual consumer ack on the read side).
- Always pair a queue with a dead-letter exchange and a TTL/length limit so failures are captured, not lost.
- Make consumers idempotent; competing consumers and redelivery mean messages can be processed more than once and out of order.
- Use exchange-to-exchange bindings to evolve topologies and to keep per-connection bindings minimal at scale.
- Prefer asynchronous messaging over synchronous calls between services; where synchronous calls are unavoidable, wrap them in retry-with-backoff and a circuit breaker.
- Use quorum queues for durable HA queues in clustered deployments.

## Anti-patterns & pitfalls
- **Assuming durable means persistent.** A durable queue without persistent messages still loses its contents on restart.
- **Fire-and-forget where reliability matters.** Auto-ack discards messages if the consumer crashes mid-processing; use manual ack.
- **No dead-letter strategy.** Rejected/expired/overflow messages vanish silently.
- **Infinite retry / dead-letter loops.** Re-routing dead letters back without watching the count creates endless reprocessing.
- **Topic exchange for everything.** Evaluating wildcard routing keys across many bindings is slower than fanout/direct; use exchange-to-exchange bindings to limit key evaluation.
- **Synchronous call chains.** Direct request/reply chains reintroduce the temporal coupling and cascading failure that messaging exists to remove.
- **API Gateway / coordinator as a single point of failure.** Centralizing routing or logic recreates the bottleneck microservices were meant to avoid. (Morlion)

## Decision guidance
- **Exchange selection:** broadcast to all → fanout; exact category → direct; flexible/open-ended categories with wildcards → topic; rich multi-attribute filtering → headers.
- **Consumption pattern:** one-of-N processing for scale → Competing Consumers; everyone gets a copy → Publish/Subscribe; need a reply → Request/Reply with a callback queue.
- **Durability:** mission-critical → durable + persistent + publisher confirms + manual ack, on quorum queues; transient/ephemeral → transient/auto-delete/exclusive queues.
- **Failure handling:** transient remote faults → retry with backoff + jitter; chronic failures → circuit breaker; unprocessable messages → dead-letter then triage/replay.
- **When messaging vs. direct calls:** prefer messaging for decoupling, buffering, and resilience; use direct calls only when an immediate response is genuinely required, and harden them.

## Source attribution
- AMQP 0-9-1 model; exchanges/queues/bindings/routing keys; the four exchange types plus default; durability vs. persistence; consumer acks and publisher confirms; dead-lettering; exchange-to-exchange bindings; priority and consumer priorities; competing consumers, pub/sub, request/reply, conversations; retry/backoff/jitter; circuit breaker; API gateway and BFF caveats; quorum queues: Morlion (2024), chs. 3–6.
- Guaranteed (store-and-forward) delivery and message correlation/sequence in coordination: Christudas (2019), ch. 15.

## Recent developments (2024–2026)

- **RabbitMQ 4.0 (Sept 2024) was a major release that reshapes this file's guidance.** Classic mirrored queues were fully removed after three years of deprecation, leaving quorum queues and streams as the two replicated data types. The file's recommendation to "use quorum queues for durable HA queues" is now effectively mandatory: mirrored queues no longer exist. (RabbitMQ blog, "Quorum Queues in 4.0," 2024; rabbitmq-server release notes 4.0.x)
- **AMQP 1.0 is now a core, always-on protocol in RabbitMQ.** As of 4.0, AMQP 1.0 is enabled by default with more than double the peak throughput of 3.13.x on some workloads, and AMQP 1.0 clients can manage topologies the way AMQP 0-9-1 clients do. The file is written around the AMQP 0-9-1 model (still valid), but practitioners should know AMQP 1.0 is now first-class on RabbitMQ. (RabbitMQ blog 2024)
- **Quorum queues gained priorities and faster recovery.** RabbitMQ 4.0 added quorum-queue priority support (normal/high, matching AMQP 1.0 semantics) and sub-linear recovery on node startup via checkpoints, closing prior gaps versus classic queues. This updates the file's priority-queue and quorum-queue notes. (RabbitMQ blog 2024)
- **RabbitMQ 4.1.0 (April 2025) optimized quorum queues and extended streams.** It lowered quorum-queue CPU usage and raised consumer throughput, and added AMQP 1.0 Filter Expressions support for streams (server-side filtering on stream consumers). Streams continue to mature as the non-destructive, replayable log alternative to queues. (RabbitMQ blog, "RabbitMQ 4.1.0 is released," 2025; CloudAMQP 2025)
- **Migration guidance is now explicit.** RabbitMQ published 2025 guidance on migrating from classic mirrored queues to quorum queues, reflecting that many production estates still had to move off the removed feature. (RabbitMQ blog, "Migrating ... to QQ," 2025)
- **Core AMQP/broker mechanics (exchanges, bindings, acks, publisher confirms, dead-lettering, competing consumers, circuit breakers) are stable.** The 2024-2026 change is consolidation onto quorum queues + streams + first-class AMQP 1.0, not new messaging patterns.

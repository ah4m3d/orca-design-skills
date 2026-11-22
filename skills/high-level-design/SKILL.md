---
name: high-level-design
description: Design and review reliable, scalable, and maintainable distributed systems using explicit requirements, data models, storage, replication, partitioning, consistency, processing, and evolution tradeoffs.
---

# High-level design

Use this skill for HLD, system-design interviews, architecture proposals, and reviews of distributed systems. Start from the guarantees the system must provide and the workload it must handle; do not select technologies before the data flow and failure model are clear.

## Architecture workflow

1. Establish scope: actors, use cases, APIs or events, read/write paths, latency and availability targets, data retention, geographic scope, privacy/security constraints, and explicit out-of-scope features.
2. Quantify the workload with rough peak and average request rates, payload sizes, fan-out, storage growth, hot-key risk, and bandwidth. State estimates and use them to justify capacity and partitioning choices.
3. Define the data model and ownership. Identify source-of-truth records, derived views, keys, indexes, access patterns, ordering requirements, and whether relationships need joins or can be denormalized. Choose storage by workload and guarantees, not fashion.
4. Draw the request and data-flow paths: clients, gateways, services, caches, queues/logs, databases, object stores, and read models. Distinguish synchronous dependencies from asynchronous propagation and label trust or failure boundaries.
5. Design reliability explicitly: redundancy, failure domains, health checks, timeouts, bounded retries with jitter, backpressure, idempotency keys, durable queues, graceful degradation, recovery point/time objectives, backups, restore tests, and observability. Explain what happens when every dependency is slow or unavailable.
6. Choose consistency and delivery semantics per operation. State whether reads are linearizable, read-your-writes, monotonic, causal, or eventually consistent; define conflict resolution, stale-read tolerance, duplicate handling, and what users observe during failover. Do not claim exactly-once processing without explaining the transaction and retry boundaries.
7. Choose replication and partitioning deliberately. Specify leader/follower or leaderless behavior, synchronous versus asynchronous replication, quorum assumptions, rebalancing, shard keys, locality, hotspot mitigation, ordering scope, and what happens when a replica, region, or partition is lost.
8. Separate commands from queries and derived data where useful. For queues, logs, batch jobs, and stream processors, define event schemas, partitions, offsets, windows, watermarks, replay, late data, poison messages, schema evolution, and whether consumers are idempotent.
9. Cover data lifecycle and change: encoding and compatibility, migrations, dual reads/writes, backfills, retention/deletion, rollbacks, versioned APIs, and how old and new clients coexist during a rolling deployment.
10. Validate the design with normal flows, overload, dependency failure, duplicate delivery, out-of-order events, partial writes, stale cache, hot partition, region loss, recovery, and schema migration. Tie each test to an invariant, SLO, or operational signal.

## Required output shape

For an HLD answer, present: requirements and assumptions; estimates; API/event contracts; data model and access patterns; component/data-flow diagram; storage and indexing choices; scaling plan; consistency and transaction boundaries; failure and recovery behavior; security and observability; deployment/evolution plan; bottlenecks and tradeoffs; and focused validation scenarios. Start with a simple design, then extend it for named follow-ups such as multi-region writes, ranking, fan-out, retention, or replay.

## Decision rules

- Reliability is a property of the whole system: availability, durability, correctness, and operability must be stated separately.
- Scalability means handling growth in load, data, and organizational complexity without a single unbounded bottleneck.
- Maintainability includes understandable data ownership, operable failure modes, compatible change, debuggable pipelines, and safe migrations.
- A cache, queue, replica, or microservice adds a failure and consistency boundary; name the benefit and the new operational cost.
- Prefer at-least-once delivery plus idempotent consumers when that is simpler and honest; make loss and duplication behavior explicit.
- Use asynchronous processing for decoupling and smoothing bursts, not to hide an undefined correctness requirement.
- Treat derived data as disposable only when it can be rebuilt from an authoritative source and the rebuild cost is acceptable.
- Avoid premature multi-region or microservice decomposition when the required guarantees and workload do not justify it.

Do not collapse HLD into a class diagram or a list of vendor products. Use low-level design only for a critical component whose internal object model affects the architecture.

---
name: otp-thinking
description: Apply when adding background processing, caching, async execution, concurrent requests, state across requests, job queues, or when the user mentions GenServer, Supervisor, Agent, Task, Registry, DynamicSupervisor, Broadway, Oban, fault tolerance, or "let it crash". Use when this capability is needed.
metadata:
  author: gissandrogama
---

# OTP Thinking

**Resumo (pt-BR):** GenServer é gargalo por design (um processo, uma mensagem por vez). Use ETS para leituras paralelas. Task.Supervisor em produção, não Task.async. Broadway = filas externas; Oban = jobs em DB.

Paradigm shifts for OTP design. These insights challenge typical concurrency and state management patterns.

## The Iron Law

```
GENSERVER IS A BOTTLENECK BY DESIGN
```

A GenServer processes ONE message at a time. Before creating one, ask: Do I need serialized access? Will this become a throughput bottleneck? Can reads bypass the GenServer via ETS?

**The ETS pattern:** GenServer owns ETS table; writes serialize through GenServer; reads bypass with `:read_concurrency`. Don't wrap stateless functions in GenServer.

## GenServer Patterns

- `call/3`: synchronous, expecting reply; use for back-pressure with appropriate timeouts
- `cast/2`: fire-and-forget
- `handle_continue/2`: post-init work—keeps `init/1` fast and non-blocking

## Task.Supervisor, Not Task.async

| Pattern | On task crash |
|---------|----------------|
| Task.async/1 | Caller crashes (linked) |
| Task.Supervisor.async_nolink/2 | Caller survives |

Use Task.Supervisor for production, graceful shutdown, observability. Use Task.async only for experiments or when crash-together is acceptable.

## DynamicSupervisor + Registry

Use Registry for names—never create atoms dynamically. `via_tuple(id) = {:via, Registry, {MyApp.Registry, id}}`. PartitionSupervisor scales for many children.

## :pg vs Registry

- **Registry:** Single node, named dynamic processes
- **:pg:** Cluster-wide process groups, pub/sub (:pg replaced :pg2)

## Broadway vs Oban

| Tool | Use For |
|------|---------|
| Broadway | External queues (SQS, Kafka, RabbitMQ)—data ingestion with batching |
| Oban | Background jobs with database persistence |

Broadway is NOT a job queue. Don't use Broadway for background jobs; don't use Oban for external queue consumption.

## Supervision Strategies

- :one_for_one: independent children
- :one_for_all: interdependent (all restart)
- :rest_for_one: sequential dependency

Set :max_restarts and :max_seconds to prevent restart loops.

## Abstraction Decision Tree

Need state? No → plain function. Yes → Complex? No → Agent. Yes → Supervision? No → spawn_link. Yes → Request/response? No → Task.Supervisor. Yes → Explicit states? No → GenServer. Yes → GenStateMachine.

## Storage

- Memory cache: ETS (`:read_concurrency`)
- Static config: :persistent_term
- Disk (small): DETS (2GB limit)
- Transactions/distribution: Mnesia

## Red Flags - STOP and Reconsider

- GenServer wrapping stateless computation
- Task.async in production when you need error handling
- Creating atoms dynamically for process names
- Single GenServer as throughput bottleneck
- Using Broadway for background jobs (use Oban)
- Using Oban for external queue consumption (use Broadway)

**Any of these? Re-read The Iron Law and the Abstraction Decision Tree.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gissandrogama) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

---
name: concurrency-android
description: Android concurrency & background work: coroutines/dispatchers, structured concurrency, WorkManager vs Services/Foreground services, and OS execution limits. Use when modifying Android app code or background processing. Use when this capability is needed.
metadata:
  author: shunta-sato
---

## Purpose

Keep UI responsive and background work reliable under Android platform constraints.

## When to use

Use this skill when touching:
- Kotlin coroutines, flows, or dispatchers
- Service / WorkManager / JobScheduler
- long-running or background work

## How to use

0) Open `references/concurrency-android.md`.
1) Decide: in-process coroutine vs WorkManager vs Foreground Service. Justify with constraints.
2) Enforce structured concurrency and cancellation paths.
3) Define failure handling and retries (idempotency).
4) Define observability (log correlation IDs; metrics for queue/latency).
5) Define verification (unit tests + instrumentation tests if needed; background restrictions).

## Output expectation

- Add an **Android Background Work Choice** section to the Concurrency Plan.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shunta-sato) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->

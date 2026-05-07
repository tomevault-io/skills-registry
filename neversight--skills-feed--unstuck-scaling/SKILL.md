---
name: unstuck-scaling
description: Use when AI agents frequently hit dead ends, when reliability is the main constraint on scaling utility, or when general model improvements don't solve specific blockers
metadata:
  author: neversight
---

# The Unstuck Scaling Framework

## Overview

A systematic approach to improving AI reliability by treating **"getting stuck"** as the primary bottleneck. Instead of broad improvements, painstakingly identify specific failure modes and create tight feedback loops.

**Core principle:** Address specific bottlenecks, not general intelligence.

## The Cycle

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                  │
│     ┌───────────────────┐                                       │
│     │  IDENTIFY         │                                       │
│     │  'Stuck' Points   │                                       │
│     │  (auth, payments) │                                       │
│     └─────────┬─────────┘                                       │
│               │                                                  │
│               ▼                                                  │
│     ┌───────────────────┐                                       │
│     │  ADDRESS          │                                       │
│     │  Specific         │                                       │
│     │  Bottlenecks      │                                       │
│     └─────────┬─────────┘                                       │
│               │                                                  │
│               ▼                                                  │
│     ┌───────────────────┐                                       │
│     │  QUANTITATIVELY   │                                       │
│     │  Tune System      │                                       │
│     │  (pass/fail rate) │                                       │
│     └─────────┬─────────┘                                       │
│               │                                                  │
│               ▼                                                  │
│     ┌───────────────────┐                                       │
│     │  FAST FEEDBACK    │─────────────────────────┐             │
│     │  Loop             │                         │             │
│     └───────────────────┘                         │             │
│               ▲                                   │             │
│               └───────────────────────────────────┘             │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

## Key Principles

| Principle | Description |
|-----------|-------------|
| **Specific blockers** | Identify exact points where AI fails |
| **Quantitative tuning** | Measure stuck rates, not vibes |
| **Fast feedback** | Rapid iteration on fixes |
| **Bottleneck focus** | Specific roadblocks > general intelligence |

## Common Mistakes

- Focusing on general model improvements
- Failing to measure "stuck" rates quantitatively
- Slow feedback loops preventing rapid iteration

---

*Source: Anton Osika (Lovable, GPT Engineer) via Lenny's Podcast*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

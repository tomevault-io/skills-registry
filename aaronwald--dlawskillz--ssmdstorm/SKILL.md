---
name: ssmdstorm
description: Multi-agent orchestration for ssmd market data system tasks. Extends waldstorm with domain-specific experts for secmaster, data feeds, trading APIs, and data quality. Use when working on connectors, exchanges, NATS pipelines, market metadata, or user says "ssmdstorm", "market data task", "exchange work". Use when this capability is needed.
metadata:
  author: aaronwald
---

# ssmdstorm

Multi-agent orchestration for ssmd market data system work. Extends waldstorm with domain-specific experts.

## Overview

ssmdstorm adds 7 ssmd-specific experts to waldstorm's general panel, plus 4 exchange domain agents from shadowagent:

**ssmd experts (dlawskillz):**

| Expert | Focus Area |
|--------|------------|
| Secmaster | Market metadata, sync, lifecycle, CDC, Redis cache |
| Data Feed | Connectors, WebSocket, NATS streams, sharding |
| Access Feed | Trading APIs, orderbook, fills, positions |
| Data Quality | NATS vs API reconciliation, trade verification, DQ Python tools |
| CLI | ssmd CLI commands (Deno ops + Go GitOps), env management |
| Symbology | DB tables, NATS subjects, SQL queries for cross-exchange data |
| Parquet Backfill | Parquet generation: ssmd-parquet-gen CLI, feed/stream/prefix mappings, K8s Jobs, GCS verification |
| Harman Deploy | Harman OMS K8s deployment, new exchange instance setup, CR configuration |
| Integration Test | Kalshi demo integration tests, harman live tests, end-to-end validation |
| ssmd Architecture | Data pipeline end-to-end: component dependencies, data flow, NATS streams, CDC, impact analysis |
| Harman Architecture | OMS end-to-end: order lifecycle, EMS/OMS split, pump model, reconciliation, exchange adapters |

**Exchange domain agents (shadowagent):**

| Agent | Focus Area |
|-------|------------|
| Kalshi | Kalshi exchange: API, fees, market mechanics, crypto tickers |
| Kraken | Kraken Futures: perpetuals, funding rates, WebSocket API |
| Polymarket | Polymarket: CLOB, Gamma API, token model, sharding |
| Symbology | Cross-exchange identifier formats, ticker anatomy |

## Expert Selection Guide

| Task Domain | ssmd Experts | + shadowagent | + General Experts | + varlab-ops |
|-------------|--------------|---------------|-------------------|--------------|
| Market metadata work | Secmaster, CLI | - | Database, Senior Dev | - |
| Adding new exchange | Data Feed | shadow:{exchange} | DevOps, Security | Operations |
| Trading API integration | Access Feed, Secmaster | shadow:{exchange} | Security, API Designer | - |
| Data pipeline architecture | ssmd Architecture | shadow:{exchange} | Senior Dev, Performance | - |
| OMS architecture decisions | Harman Architecture | shadow:{exchange} | Security, Senior Dev | - |
| Pipeline deployment | Data Feed, CLI | - | DevOps, Platform/Infra | Operations |
| Signal development | Data Feed, Secmaster, CLI | shadow:{exchange} | Performance, QA | - |
| CDC/cache work | Secmaster | - | Database, Performance | - |
| Orderbook integration | Access Feed, Data Feed | shadow:{exchange} | Performance, Security | - |
| CLI operations | CLI | - | DevOps | - |
| Data quality checks | Data Quality, CLI | shadow:{exchange} | QA | - |
| Parquet backfill/regen | Parquet Backfill, Data Quality | - | DevOps | Operations |
| Ticker/ID mapping | Symbology | shadow:symbology | Database | - |
| Cross-exchange correlation | Symbology, Data Feed | shadow:symbology | Performance | - |
| End-to-end deploy (code + K8s) | Data Feed, CLI | shadow:{exchange} | Security, Performance | Operations |

### Cross-Skill Pairing

Tasks that span code AND infrastructure need experts from **both** ssmdstorm and varlab-ops. The selection guide above includes the varlab-ops column for these cases. Common cross-skill patterns:

| Pattern | Why Both Skills |
|---------|----------------|
| New exchange end-to-end | ssmd: connector code, NATS subjects, writer. varlab-ops: K8s deployment, NATS stream, archiver CR, network policy |
| Image build + deploy | ssmd: Rust/Deno changes, tag format. varlab-ops: deployment YAML, Flux reconcile, image pull |
| Scale operations | ssmd: CLI commands. varlab-ops: Flux suspend/resume, kubectl context |

### Trigger Keywords

When analyzing a task description, match these keywords to experts:

| Keywords | Expert |
|----------|--------|
| websocket, connector, exchange, feed, subscribe, channel | Data Feed |
| secmaster, market metadata, sync, CDC, Redis, lifecycle | Secmaster |
| orderbook, fill, position, trading, order, balance | Access Feed |
| nats count, match rate, reconciliation, missing trades, dq | Data Quality |
| cli, scale, deploy, env, schedule, deno task | CLI |
| ticker, symbol, pair_id, token_id, condition_id, identifier, cross-exchange | Symbology |
| parquet, backfill, regenerate, parquet-gen, jsonl to parquet | Parquet Backfill |
| harman deploy, new harman instance, harman CR, exchange instance | Harman Deploy |
| integration test, demo API, live test, harman test, end-to-end test | Integration Test |
| architecture, component dependency, impact analysis, data flow, pipeline design | ssmd Architecture |
| order lifecycle, EMS, OMS, pump model, reconciliation, exchange adapter, session model | Harman Architecture |
| kalshi, prediction market, contract, series, category, CFTC | shadow:kalshi |
| kraken, perpetual, funding rate, PF_, FF_, futures | shadow:kraken |
| polymarket, CLOB, condition_id, token, Gamma API, UMA | shadow:polymarket |
| ticker format, pair anatomy, cross-exchange mapping | shadow:symbology |
| mcp, api key, data access, market lookup, datasets:read, query parquet | Symbology, Data Feed |
| deployment, kustomization, flux, network policy, PVC | Operations (varlab-ops) |
| securityContext, input validation, sanitization, auth | Security (waldstorm) |
| max_message_size, buffer, latency, throughput, memory | Performance (waldstorm) |

## Instructions

### Step 1: Understand the Task

Same as waldstorm - gather task description, constraints, context.

### Step 2: Select Experts

**Always include at least one ssmd expert.** Use the selection guide above.

**MANDATORY:** Always include the **QA/Testing Expert** (`dlaw:qa-testing-expert`) in every expert panel. Complete unit test coverage is required for all code changes.

**Agent definitions** in `agents/` provide each ssmd expert as a spawnable agent with `memory: local` for persistent learnings across sessions.

Available ssmd agents (dlawskillz, in `./agents/`):
- `ssmd-secmaster` - Market metadata, sync, CDC
- `ssmd-data-feed` - Connectors, NATS, archiving
- `ssmd-access-feed` - Trading APIs, orderbook, fills
- `ssmd-data-quality` - NATS vs API reconciliation, trade verification, DQ Python tools
- `ssmd-cli` - ssmd CLI commands (Deno ops + Go GitOps), env management
- `ssmd-symbology` - DB tables, NATS subjects, SQL queries for cross-exchange data
- `ssmd-parquet-backfill` - Parquet generation CLI, feed/stream/prefix mappings, K8s Jobs, GCS verification
- `ssmd-harman-deploy` - Harman OMS K8s deployment, new exchange instance setup
- `ssmd-integration-test` - Kalshi demo integration tests, harman live tests, end-to-end validation
- `ssmd-architecture` - Data pipeline architecture: component deps, data flow, NATS, CDC, impact analysis
- `harman-architecture` - OMS architecture: order lifecycle, EMS/OMS, pump, reconciliation, exchange adapters

Available exchange agents (shadowagent, `shadow:` prefix):
- `shadow:kalshi` - Kalshi exchange: API, fees, market mechanics, crypto tickers
- `shadow:kraken` - Kraken Futures: perpetuals, funding rates, WebSocket API
- `shadow:polymarket` - Polymarket: CLOB, Gamma API, token model, sharding
- `shadow:symbology` - Cross-exchange identifier formats, ticker anatomy

Combine with waldstorm's general agents (Security, DevOps, etc.) as needed.

### Step 3-9: Follow waldstorm

Use team primitives (TeamCreate, TaskCreate, Task tool for teammates, SendMessage) to run expert analysis, then synthesize, plan, and execute per waldstorm workflow. This includes:

- **Step 6 (ECC Code Review):** After synthesis, optionally invoke `everything-claude-code:code-review` for an independent structured review of proposed changes. Recommended for any task touching data pipelines, trading APIs, or security-sensitive code.
- **Step 7 (Writing Plans):** Use `superpowers:writing-plans` to save implementation plan
- **Step 8 (Executing Plans):** Use `superpowers:executing-plans` with checkpoints
- **Step 9 (Cleanup):** shutdown_request + TeamDelete

### Step 8: Track Records (automated via agent memory)

Agents with `memory: local` automatically read/write their own MEMORY.md in `.claude/agent-memory-local/<name>/MEMORY.md`. Manual track record updates are no longer needed when using agents.

The **Expert Track Record** table below serves as historical reference. Agents now self-update their learnings via `memory: local`.

## Expert Track Record

Track which experts produced actionable findings per task type. Use this to inform future selection.

| Session | Task | Experts Used | Key Findings |
|---------|------|-------------|--------------|
| 2026-02-06 | Kraken exchange (end-to-end) | Data Feed, Operations, Security, Performance | Security: missing securityContext, NATS subject injection via unsanitized symbols. Performance: WebSocket max_message_size default too large. Data Feed: archiver-per-exchange, serde untagged pitfalls. Operations: static Deployment vs CR, Flux suspend lifecycle. All 4 experts produced HIGH-priority findings. |

**Observations:**
- End-to-end exchange tasks need 4+ experts across skills (cross-skill pairing essential)
- Security and Performance generals caught infra/config issues the domain experts missed
- Data Feed expert most valuable for exchange-specific protocol details
- Operations expert essential whenever K8s manifests are involved

## Key ssmd Context

**Architecture:**
```
Kalshi WS  -> Connector -> NATS JetStream -> Archiver/Signal/Notifier
Kraken WS  -> Connector ---^
                              |
PostgreSQL <- secmaster sync <- CDC -> dynamic subscriptions
```

**Current state (Feb 2026):**
- Exchanges: Kalshi (prediction markets), Kraken Futures (crypto perps), Polymarket (prediction markets)
- Kalshi channels: `ticker`, `trade`, `market_lifecycle_v2`
- Kraken channels: `ticker`, `trade`
- Polymarket channels: `last_trade_price`, `price_change`, `book`, `best_bid_ask`
- Pending: `orderbook_delta`, `fill`, `market_positions` (Kalshi)
- Environments: prod (GKE Autopilot, us-east1)
- **ssmd-mcp**: Local Python MCP server with 6 tools (`query_trades`, `query_prices`, `query_raw`, `lookup_market`, `list_feeds`, `check_freshness`) — DuckDB for GCS parquet, httpx for data-ts API
- **data-ts LoadBalancer**: External access from allowed CIDRs, `externalTrafficPolicy: Local`

**Key paths:**
- ssmd code: project root
- K8s manifests: `clusters/gke-prod/apps/ssmd/` (in 899bushwick)
- CLI reference: `docs/reference/cli-reference.md` (in 899bushwick)

## Model Routing

Inherit waldstorm's model routing table. Additional ssmd-specific guidance:

| Context | Model | Rationale |
|---------|-------|-----------|
| ssmd domain experts (data-feed, secmaster, etc.) | sonnet | Domain analysis is focused and well-scoped |
| Exchange agents (shadow:kalshi, etc.) | sonnet | Protocol-specific analysis |
| Cross-expert synthesis with domain conflicts | opus | Requires deep understanding of ssmd architecture |
| Parquet backfill / CLI operations | haiku | Mechanical, well-documented procedures |

## Skills & Plugins Used

- `superpowers:writing-plans` - Convert recommendations to implementation plan
- `superpowers:executing-plans` - Execute with review checkpoints
- `everything-claude-code:code-review` - Independent structured review of proposed changes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aaronwald) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

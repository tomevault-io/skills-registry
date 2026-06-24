---
name: nottmemory-review
description: Read-only telemetry dashboard for the memory subsystem. Surfaces top queries, hit/miss rate, latency p50/p95/p99 (db_ms via Rust + hook_ms via JSONL), top-N memories injected vs ignored, and scope/tier breakdown. Trigger on 'memory review', 'memory audit', 'memory stats', 'how is memory doing', 'memory hit rate', 'memory latency'. Do not trigger for memory mutations (create/touch/archive) — this skill never writes. Use when this capability is needed.
metadata:
  author: menot-you
---

> **Note (public `nott` plugin):** `task()`/`doc()`/`status` calls below map to local `.nott/memory/` files when SSOT MCP is not registered. See [docs/MEMORY-PROTOCOL.md](../../docs/MEMORY-PROTOCOL.md) for the field-by-field mapping.

# /nott:memory-review — memory telemetry dashboard

## context

When to use:
- Validate hit rate before tuning `memory-trigger` keyword patterns
- Spot ignored memories (injected but never touched) — candidates for `archive`/`supersede`
- Diagnose latency regressions — separate Python+RPC overhead from DB time
- Gather evidence for Phase 2 followups (`memory-pgvector-phase2`, `memory-fsrs6-pluggable`, `memory-stdio-transport`)
- Periodic dogfood self-check

When NOT to use:
- Reading a single memory → `memory(action="search", id=...)` directly
- Mutating memory state — this skill is read-only by design
- Cross-guild aggregation — scope is current guild only

Anti-patterns:
1. Treating output as ground truth without checking sample sizes (`samples<10` = noise)
2. Comparing latency without normalizing windows (default 7d vs `--last all`)
3. Drawing FSRS-6 / pgvector conclusions from <100 search samples

## role

Read-only aggregator over `audit_log` (memory.*.timing entries) + `memories` table. Never writes. Two data sources surfaced separately so overhead is visible:

| metric | source | what it measures |
|---|---|---|
| **db_ms** (p50/p95/p99) | `audit_log` via `memory.{action}.timing` detail JSON | Rust action time only (DB query) |
| **hook_ms** | `.nott/hooks/audit/hooks.jsonl` `elapsed_ms` field | End-to-end UX (Python boot + RPC + DB) |

`hook_ms − db_ms` = overhead. If sustained `>50ms` p50 → open task `memory-rpc-overhead-investigate`.

## task

1. Parse args (all optional):
   - `--last 7d|30d|all|<seconds>` (default `7d`)
   - `--scope project|user|feedback|reference|<custom>|all` (default `all`)
   - `--top N` (default `10`, max `100`)

2. Call the review action:

   ```
   mcp__ssot__memory(
       action="review",
       last=<parsed>,
       scope=<parsed_or_omitted>,
       top=<parsed>
   )
   ```

   Returns shape:
   ```json
   {
     "window_secs": 604800,
     "cutoff_epoch": 1747094400,
     "project_id": "...",
     "scope": null,
     "totals": { "total": N, "active": N, "archived": N, "pinned": N },
     "latency": [ { "action": "memory.search.timing", "samples": N, "p50_ms": ..., "p95_ms": ..., "p99_ms": ..., "min_ms": ..., "max_ms": ... }, ... ],
     "hit_rate": { "total_searches": N, "hits": N, "misses": N, "hit_rate": 0.XX },
     "top_queries": [ { "query": "...", "count": N, "hits": N, "misses": N }, ... ],
     "top_injected": [ { "id": "mem-...", "scope": "...", "insight": "...", "last_used": epoch, "strength": F, "retention": F, "tier": "..." }, ... ],
     "top_ignored": [ /* same shape as top_injected */ ],
     "scope_breakdown": [ { "scope": "...", "total": N, "active": N, "essential": N, "active_tier": N, "dormant": N, "archive_candidate": N }, ... ],
     "latency_ms": N
   }
   ```

3. Read `.nott/hooks/audit/hooks.jsonl` (last 10k lines tail) and aggregate `memory-trigger` / `memory-inject` / `memory-touch` entries by hook. Compute hook_ms p50/p95/p99 per hook within the same window. Tail with:

   ```bash
   tail -10000 .nott/hooks/audit/hooks.jsonl 2>/dev/null | jq -c 'select(.hook | startswith("memory-"))'
   ```

   Process those entries inside `mcp__plugin_context-mode_context-mode__ctx_execute` (don't pull raw lines into context). Filter to `ts >= cutoff_epoch`.

4. Format a Markdown report. Sections (in this order):

   ### Overview
   - Window: `last 7d` (or whatever was parsed)
   - Totals: `total / active / archived / pinned`
   - Scope filter (if any)

   ### Latency
   Table with columns: `action | samples | db p50 | db p95 | db p99 | hook p50 | hook p95 | hook p99`.
   Add a footnote line: `overhead p50 = hook_p50 - db_p50`. Flag rows where `samples < 10` with `⚠ low sample`.

   ### Hit rate
   - Total searches in window
   - Hits / Misses / Hit-rate %
   - Flag if `hit_rate < 0.5` AND `total_searches >= 50` → "Phase 2 trigger candidate (memory-fsrs6-pluggable evidence)"

   ### Top queries
   Table: `query | count | hits | misses`. Truncate query to 80 chars.

   ### Top injected (high-value memories)
   Table: `id (8 chars) | scope | tier | retention | insight (60 chars)`.

   ### Top ignored (never touched in window)
   Table: same shape as injected, sorted by `last_used` ascending. Suggest action: "Candidates for `memory(action='archive')` or `memory(action='supersede')`."

   ### Scope breakdown
   Table: `scope | total | active | essential | active_tier | dormant | archive_candidate`.

5. End with a 1-line `EVIDENCE GATES` summary based on observed metrics:
   - `pgvector_phase2`: ✅ ready / ⏸ insufficient (need corpus >1k, current = N)
   - `fsrs6_pluggable`: ✅ ready / ⏸ insufficient (need hit_rate <50% over >100 searches, current = X% over Y)
   - `stdio_transport`: ✅ ready / ⏸ insufficient (need p99 hook_ms >100ms over >100 samples, current = Xms)

   Each gate's "ready" emits a follow-on suggestion: `task(action="update", id="<task_id>", status="doing")` for the corresponding SSOT followup.

## output

A Markdown report rendered as the final message. Never write the report to disk (no side effects). Never call `mcp__ssot__memory` with any action other than `review`. If the user asks to fix something based on the report, present concrete commands they (or a follow-up agent) can run — don't execute them from this skill.

---
> Source: [menot-you/claude](https://github.com/menot-you/claude) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->

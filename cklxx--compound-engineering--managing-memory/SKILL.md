---
name: managing-memory
description: Use when a VERIFIED, DURABLE insight emerges — not raw events (that's recording-practices), not pattern analysis (that's running-retros). Persists one-line knowledge items to docs/memory/long-term.md, which is loaded on every session start.
metadata:
  author: cklxx
---

# Managing Memory

**Role in the loop:** PERSIST distilled knowledge that survives across sessions. This is the agent's long-term brain. It does NOT log raw events (that's `recording-practices`) or analyze patterns (that's `running-retros`).

**The critical distinction:**
- `recording-practices` writes: "2026-02-09 — nil map panic in config loader" (raw event, full detail)
- `managing-memory` writes: "[2026-02-09] Always initialize maps in struct constructors — nil maps panic on write (see error-experience/2026-02-09-nil-map-panic.md)" (one-line rule, linked to evidence)

## Precise Trigger Conditions

Activate this skill ONLY when ALL three conditions are met:

| Condition | Test |
|-----------|------|
| **Verified** | You've seen this work or fail at least once, with concrete evidence |
| **Durable** | This will still be true and useful in 2+ weeks |
| **Actionable** | The knowledge tells you what to DO, not just what IS |

**Examples that PASS the test:**

| Knowledge | Why it qualifies |
|-----------|-----------------|
| "Always run `go vet` before commit — caught 3 bugs this week" | Verified (3 bugs), durable (always true), actionable (run go vet) |
| "Use `sync.RWMutex` not `sync.Mutex` for read-heavy caches" | Verified (caused latency spike), durable (performance principle), actionable (use RWMutex) |
| "API returns 500 not 404 for deleted resources — needs custom error handler" | Verified (hit in prod), durable (API behavior), actionable (add handler) |

**Examples that FAIL the test:**

| Knowledge | Why it fails |
|-----------|-------------|
| "Fixed a typo in README" | Not durable, not actionable |
| "Go is faster than Python" | Not actionable, too vague |
| "We should probably refactor the auth module" | Not verified, no evidence |
| "The deploy took 5 minutes today" | Not durable, not actionable |

## Memory File Structure

`docs/memory/long-term.md`:

```markdown
# Long-term Memory

> Updated: YYYY-MM-DD HH:MM

## Active Memory

### Architecture & Design
- [YYYY-MM-DD] <one-line actionable knowledge>

### Conventions & Patterns
- [YYYY-MM-DD] <one-line actionable knowledge>

### Technical Decisions
- [YYYY-MM-DD] <decision> — rationale: <why> (see docs/plans/...)

### Known Pitfalls
- [YYYY-MM-DD] <pitfall> — prevention: <what to do> (see error-experience/...)

### Environment & Config
- [YYYY-MM-DD] <one-line actionable knowledge>
```

## Operations

### PERSIST — Add new knowledge

1. Check if a similar item already exists → update it instead of duplicating
2. Write ONE line under the appropriate section
3. Link to evidence: `(see error-experience/YYYY-MM-DD-slug.md)` or `(see plans/YYYY-MM-DD-slug.md)`
4. Update the `Updated:` timestamp at the top

**Format:** `- [YYYY-MM-DD] <imperative actionable statement> (see <evidence link>)`

**Concrete example:**
```markdown
### Known Pitfalls
- [2026-02-09] Always initialize maps in Go struct constructors — nil map assignment panics at runtime, not compile time (see error-experience/2026-02-09-nil-map-panic.md)
```

If the item needs more than one line of explanation → create `docs/memory/topics/<topic>.md` and put a pointer in `long-term.md`:
```markdown
### Technical Decisions
- [2026-02-09] Chose token bucket for rate limiting over sliding window — see memory/topics/rate-limiting.md
```

### LOAD — Session startup

Executed by `using-compound-engineering` skill, not directly by this skill.

### UPDATE — Correct existing knowledge

When knowledge changes:
```markdown
- [2026-01-15] Use Redis for caching → [2026-02-09] Updated: Use in-memory cache for single-instance, Redis only for multi-instance
```

When knowledge was wrong:
```markdown
- ~~[2026-01-15] sync.Pool is always faster for allocations~~ → [2026-02-09] Corrected: sync.Pool only helps when allocation rate is high AND objects are large
```

### CURATE — Prune stale knowledge

Triggered by `running-retros` or when items exceed 50:

1. For each item, score: **recency** (referenced in last 30 days?) × **impact** (prevented a bug or saved time?) × **relevance** (still applies to current codebase?)
2. Items scoring low on ALL three → move to `docs/memory/archive/YYYY-MM-archive.md`
3. Target: **30–50 active items**. Below 30 is fine. Above 50 means you're not curating enough.

## Key Principles

- **One line per item.** If you need more, use a topic file.
- **Link to evidence.** Every item traces to a plan, entry, or decision.
- **Imperative voice.** "Always do X" or "Never do Y" — not "X is related to Y."
- **Curate ruthlessly.** 40 sharp items beat 200 vague ones.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cklxx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

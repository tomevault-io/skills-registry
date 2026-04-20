---
name: plan-status
description: Check PRD progress and determine next steps for the Warrior trading system. Use when user asks "what's next", wants project status, or starting a new session. Use when this capability is needed.
metadata:
  author: matthewchung74
---

# Plan Status Skill

Check PRD progress and determine next steps for the Warrior trading system.

## When to Use

- User asks "what's next" or "next step"
- User wants to know project status
- User asks about PRD progress
- Starting a new session and need context

## Instructions

### Step 1: Read the PRD

Read the project requirements document:

```
Read /Users/mattc/Desktop/warrior/prd.md
```

The PRD defines three repos with tasks:
- **Repo C (candle-patterns)**: C1-C3 (VWAP exits, ORB disabled, ABCD pattern)
- **Repo A (ibkr-scanner-core)**: A0-A7 (scanner, enrichment, watchlist, patterns, trading, orchestrator)
- **Repo B (trading-webapp)**: B0-B7 (scaffolding, websocket, scanner/watchlist/charts/journal tabs, ops, orchestrator controls)

### Step 2: Check Repo C (candle-patterns)

Verify pattern library implementation:

```bash
# Check VWAP exit (C1)
grep -l "vwap_cross\|check_vwap" /Users/mattc/Desktop/warrior/candle-patterns/candle_patterns/*.py

# Check ORB disabled by default (C2)
grep -A5 "enabled.*False" /Users/mattc/Desktop/warrior/candle-patterns/candle_patterns/opening_range_retest.py

# Check ABCD pattern (C3)
ls /Users/mattc/Desktop/warrior/candle-patterns/candle_patterns/abcd.py
```

### Step 3: Check Repo A (ibkr-scanner-core)

Verify scanner-core implementation:

```bash
# Check core modules exist
ls /Users/mattc/Desktop/warrior/ibkr-scanner-core/ibkr_scanner_core/{models,logic,ibkr,deepseek,pipeline,patterns,trading}/ 2>/dev/null | head -20

# Check orchestrator (A7)
ls /Users/mattc/Desktop/warrior/ibkr-scanner-core/ibkr_scanner_core/orchestrator.py

# Check probes exist
ls /Users/mattc/Desktop/warrior/ibkr-scanner-core/probes/

# Run tests to verify implementation
cd /Users/mattc/Desktop/warrior/ibkr-scanner-core && .venv/bin/python -m pytest tests/ --ignore=tests/integration/ -q 2>&1 | tail -5
```

### Step 4: Check Repo B (trading-webapp)

Verify webapp implementation:

```bash
# Check routers (B1-B7 backends)
ls /Users/mattc/Desktop/warrior/trading-webapp/routers/

# Check for orchestrator API (B7 backend)
grep -l "orchestrator" /Users/mattc/Desktop/warrior/trading-webapp/routers/*.py

# Check for orchestrator UI (B7 frontend)
grep -c "orchestrator\|/api/orchestrator" /Users/mattc/Desktop/warrior/trading-webapp/templates/index.html

# Check database exists (B5)
ls /Users/mattc/Desktop/warrior/trading-webapp/trades.db
```

### Step 5: Generate Status Report

Create a status table:

| Section | Status | Notes |
|---------|--------|-------|
| **Repo C** | | |
| C1 VWAP exits | ✓/✗ | |
| C2 ORB disabled | ✓/✗ | |
| C3 ABCD pattern | ✓/✗ | |
| **Repo A** | | |
| A0-A6 Core modules | ✓/✗ | |
| A7 Orchestrator | ✓/✗ | |
| A7 Tests passing | ✓/✗ | X/Y tests |
| **Repo B** | | |
| B0-B1 Scaffolding/WS | ✓/✗ | |
| B2 Scanner tab | ✓/✗ | Backend + UI |
| B3 Watchlist tab | ✓/✗ | Backend + UI |
| B4 Charts tab | ✓/✗ | Backend + UI |
| B5 Journal tab | ✓/✗ | Backend + UI + DB |
| B6 Ops | ✓/✗ | Optional |
| B7 Orchestrator | ✓/✗ | Backend + UI |

### Step 6: Identify Next Step

Based on PRD build order:
1. Repo C first (pattern library)
2. Repo A second (scanner-core)
3. Repo B last (webapp)

Within each repo, tasks should be done in order (C1→C2→C3, A0→A7, B0→B7).

Report:
1. First incomplete task in the build order
2. What specifically needs to be done
3. Which files to create/modify

## Output Format

```
## PRD Status

[Status table]

## Next Step

**Task**: [PRD section, e.g., "B7 - Orchestrator Controls UI"]

**What's needed**:
- [Specific items from PRD]

**Files to modify**:
- [File paths]

**Suggested approach**:
- [Brief implementation notes]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matthewchung74) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

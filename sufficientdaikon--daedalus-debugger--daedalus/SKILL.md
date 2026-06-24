---
name: daedalus
description: > Use when this capability is needed.
metadata:
  author: SufficientDaikon
---

# Daedalus — Skill Entry Point

This SKILL.md is the legacy trigger entry point for the Daedalus environment
debugger. The full agent definition lives in `daedalus.agent.md` in this
same directory.

## Quick Reference

**Full diagnostic:**

```
@daedalus Run a full diagnostic and give me report.html
```

**Targeted:**

```
@daedalus Debug my github MCP server
@daedalus Pre-flight check before coding session
@daedalus My GPU is slow — find the bottleneck
@daedalus Compare today's environment with ./session-old.json
```

## Phases

| Phase | Tool                            | What                                 |
| ----- | ------------------------------- | ------------------------------------ |
| 0     | `debug_detect_environment`      | IDE, model, OS, shell, extensions    |
| 1     | `debug_probe_hardware` + stress | CPU/RAM/GPU/disk benchmarks          |
| 2     | `debug_test_all_mcp_servers`    | Connection + all tools per server    |
| 3     | `debug_probe_network`           | Latency to Anthropic/OpenAI/etc.     |
| 4     | `debug_record_model_benchmark`  | Self-measured latency + capabilities |
| 5     | `debug_scan_issues`             | Classify + score (0–100)             |
| 6     | `debug_generate_report`         | Write self-contained report.html     |

## Output

Always produces `report.html` — self-contained, no external CDN.
Health score: 100 − (CRITICAL×20) − (HIGH×10) − (MEDIUM×5) − (LOW×1).

See `README.md` for installation, `daedalus.agent.md` for full protocol.

---

## Phase 0 — SELF-DETECTION (always run first)

Before testing anything, identify your own context using available tools:

```
detect_environment() → {
  ide:    "vscode" | "cursor" | "windsurf" | "claude-desktop" | "terminal" | unknown
  model:  "claude-opus-4-6" | "claude-sonnet-*" | "gemini-*" | "gpt-*" | "local/*" | unknown
  os:     "windows-11" | "ubuntu-*" | "macos-*"
  shell:  "powershell" | "bash" | "zsh"
  node:   semver string
  python: semver string | null
}
```

**Eval-awareness pattern** (the BrowseComp technique, applied legitimately):
When you hit repeated failures during testing, STOP and reason about the
failure ITSELF before trying more tests. Ask:

- Is this a capability limit of the current model?
- Is this a constraint of the environment I'm running in?
- Is there a different tool/path that achieves the same result?
  Enumerate alternatives systematically, then proceed.

---

## Phase 1 — ENVIRONMENT AUDIT

Run ALL of these in parallel where possible:

### 1A — IDE & Extension Probe

- Detect active IDE from env vars: `TERM_PROGRAM`, `VSCODE_PID`, `CURSOR_*`, `WINDSURF_*`
- Check for `settings.json` and `keybindings.json` locations
- Enumerate installed extensions (via `code --list-extensions` if available)
- Detect conflicting extensions (e.g., both GitHub Copilot AND Claude Code)
- Check MCP config: `claude_desktop_config.json`, `.vscode/mcp.json`

### 1B — Model/API Probe

- Identify current model from system prompt or env vars
- Test model capabilities: tool use, structured output, long context, code execution
- Measure: first-token latency, tokens/sec (rough estimate), context window behavior
- If Ollama present: `ollama list`, test local model endpoints
- If multiple providers configured: test each and compare

### 1C — MCP Server Audit

For each configured MCP server:

1. Attempt connection
2. Call `list_tools` — verify response
3. Call each tool with minimal valid input
4. Measure response time
5. Categorize: HEALTHY / DEGRADED / BROKEN / UNREACHABLE

### 1D — Hardware Probe

```
cpu:  vendor, cores, freq, current_load (%)
ram:  total_gb, used_gb, available_gb, swap_used
gpu:  vendor, model, vram_gb, driver_version, utilization (%)
disk: read_mb_s, write_mb_s, free_gb
```

Use system tools: `wmic` (Windows), `lscpu`/`nvidia-smi` (Linux), `system_profiler` (macOS)

---

## Phase 2 — STRESS TESTING

### 2A — Model Stress Tests (run in sequence)

1. **Latency baseline**: 5× simple "hello" → measure p50/p95/p99
2. **Tool call stress**: Call 10 tools in sequence → measure total time
3. **Parallel tool calls**: Call 5 tools simultaneously → detect rate limits
4. **Long context**: Send 10K token input → measure degradation
5. **Code generation**: Generate + execute 5 non-trivial code snippets

### 2B — MCP Stress Tests

For each HEALTHY MCP server:

1. Call every tool 3× with valid params
2. Call every tool 1× with edge-case/boundary params
3. Intentionally call with INVALID params → verify error messages are useful
4. Concurrent calls: 5 simultaneous requests → check for race conditions

### 2C — Hardware Stress (brief, non-destructive)

- CPU: Run a 5-second computation benchmark (prime sieve)
- RAM: Allocate/free 512MB of arrays, measure peak usage
- GPU (if available): Run a matrix multiplication benchmark via Python/numpy

---

## Phase 3 — ISSUE CLASSIFICATION

For every detected issue, classify it:

```typescript
interface Issue {
  id: string; // "ISS-001"
  severity: "CRITICAL" | "HIGH" | "MEDIUM" | "LOW" | "INFO";
  category: "config" | "performance" | "compatibility" | "missing" | "broken";
  component: string; // "mcp-server:github" | "extension:copilot" | "hardware:gpu"
  title: string; // Short description
  detail: string; // Full explanation
  evidence: string[]; // Actual output/errors that prove this issue
  fixable: boolean; // Can be fixed automatically
}
```

**Severity rules**:

- CRITICAL: Agent cannot function (MCP server down, model unreachable)
- HIGH: Significant performance impact or broken feature
- MEDIUM: Degraded experience, workaround exists
- LOW: Minor inefficiency or suboptimal config
- INFO: Observations that aren't problems

---

## Phase 4 — FIX PLANS

For each CRITICAL and HIGH issue, generate a concrete fix plan:

```typescript
interface FixPlan {
  issue_id: string;
  strategy: "immediate" | "scheduled" | "manual-only";
  steps: FixStep[];
  estimated_time: string; // "2 minutes" | "10 minutes" | "requires restart"
  risk: "safe" | "requires-restart" | "data-loss-risk";
  commands: string[]; // Exact commands to run
  verification: string; // How to verify it worked
}
```

If `fixable: true`, output the exact commands with copy-pasteable formatting.
Group fixes by "do these together" vs "do these separately" (restart dependencies).

---

## Phase 5 — REPORT GENERATION

Generate `report.html` using the HTML Report Template (see `/templates/report-template.html`).

### Report must contain:

1. **Hero section**: Environment fingerprint, overall health score (0-100), timestamp
2. **Executive summary**: 3-sentence plain-English summary of what was found
3. **Component status grid**: Visual grid of all tested components with status badges
4. **Hardware dashboard**: CPU/RAM/GPU/disk with visual gauges
5. **MCP server table**: Each server, tools count, latency, status
6. **Model benchmark panel**: Latency charts, capability matrix
7. **Issues list**: Sorted by severity, collapsible details, copy-paste fix commands
8. **Fix roadmap**: Grouped by effort (Quick wins / This week / Long term)
9. **Raw evidence**: Collapsible sections with actual command output

### Scoring algorithm:

```
base_score = 100
- CRITICAL issues: -20 each (max -60)
- HIGH issues:     -10 each (max -30)
- MEDIUM issues:   -5 each  (max -10)
- LOW issues:      -1 each  (max -5)
+ performance bonuses if all latencies under threshold
score = clamp(base_score, 0, 100)
```

---

## Tool Usage Priority

Use these tools in this order when available:

1. `debug_detect_environment` — always first
2. `debug_probe_hardware` — run early, results needed for context
3. `debug_test_mcp_server` — per configured server
4. `debug_test_model_capabilities` — test current model
5. `debug_run_stress_test` — stress each component
6. `debug_scan_issues` — analyze all collected data
7. `debug_generate_report` — final step only

---

## Constraints

- Never run destructive operations (no `rm`, no registry edits, no installs) without explicit permission
- Cap individual stress tests at 30 seconds each
- If hardware stress would push CPU > 90% for more than 5 seconds, skip it and note as "skipped — resource protection"
- Always produce the report even if some phases failed — partial data > no data
- Report file must be entirely self-contained (no external CDN, inline everything)

---
> Source: [SufficientDaikon/daedalus-debugger](https://github.com/SufficientDaikon/daedalus-debugger) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->

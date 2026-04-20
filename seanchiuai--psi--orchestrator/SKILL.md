---
name: orchestrator
description: Multi-phase loop that runs Claude Code sessions with signal detection and state management Use when this capability is needed.
metadata:
  author: seanchiuai
---

# Orchestrator

The core loop in `src/psi/orchestrator.py`. Runs sequential phases, each as a series of Claude Code subprocess calls.

## Overview

`Psi` class coordinates everything:
- Validates project files exist (CLAUDE.md, PRD.md, development-progress.yaml, phase-1.md)
- Iterates phases from `development-progress.yaml`
- Each iteration: builds prompt, calls `claude -p` via stdin, parses output for signals
- Between phases: runs Tier 2 skill sync, generates next phase prompt

## Key Patterns

### Subprocess Invocation

Prompts are passed via **stdin**, not as CLI args (avoids OS arg length limits):

```python
result = subprocess.run(
    ["claude", "-p",
     "--allowedTools", "Bash,Read,Write,Edit,Glob,Grep,WebFetch,WebSearch,mcp__context7__resolve-library-id,mcp__context7__query-docs"],
    input=prompt,          # stdin, not positional arg
    capture_output=True,
    text=True,
    timeout=self.timeout,
    cwd=str(self.project_root),
)
```

### Documentation-First for Integrations

Prompt instructs Claude to search for docs before implementing any integration:
- `WebSearch` for general doc discovery
- `mcp__context7__resolve-library-id` + `mcp__context7__query-docs` for library-specific docs
- Never guess API signatures — verify first

### Signal Detection

Two signals parsed from Claude's stdout:
- `<promise>PHASE N COMPLETE</promise>` — phase done, trigger Tier 2 sync
- `<phase-blocked>N</phase-blocked>` — phase blocked, stop and report

### YAML Read/Write (PyYAML)

Always use safe variants per PyYAML docs:
- `yaml.safe_load()` for reading (prevents arbitrary object construction)
- `yaml.safe_dump()` for writing (restricts output to safe types)
- `default_flow_style=False` for block-style output
- `sort_keys=False` to preserve insertion order

PhaseManager re-reads from disk every call (no caching) since Claude modifies the file externally.

### Prompt Composition Per Iteration

Each iteration gets a freshly augmented prompt:
```
base_prompt (from phase-N.md or generated)
+ Tier 2 expertise patterns (top 20, high confidence first)
+ Tier 1 learnings (last 20, unvalidated)
```

### Docker Pitfalls

- `environment:` in docker-compose overrides `env_file` — don't duplicate vars in both
- Web subprocess needs `--allowedTools` — Claude asks permission without it
- Prompts need explicit autonomy instruction — Claude defaults to asking confirmation
- MCP tools don't work in Docker — only built-in tools available
- Initialize LEARNINGS.md before iteration loop in web flow
- SQLite needs WAL mode + busy_timeout for multi-process writes

### State Persistence

`.psi/state.json` tracks current phase/iteration. `.psi/logs/phase_X_iter_Y.json` stores per-iteration results with output preview and stderr.

## Phase Lifecycle

1. `set_status(N, "in-progress")` — update YAML via `yaml.safe_dump()`
2. Loop iterations until signal or max reached
3. If complete: run skill sync, `set_status(N, "complete")`, generate next prompt
4. If blocked or max reached: stop loop

## Related Files

- `src/psi/orchestrator.py` — Psi class
- `src/psi/phase_manager.py` — YAML state transitions (`yaml.safe_load`/`yaml.safe_dump`)
- `src/psi/prompt_generator.py` — Prompt building + learning injection
- `src/psi/weave_logger.py` — Observability tracing

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seanchiuai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

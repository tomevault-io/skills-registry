---
name: ralph-run
description: Start the Ralph Ultra autonomous development loop. Iterates through PRD stories with smart failure handling, skill-based verification, and checkpointing. Use when ready to execute a PRD plan autonomously. Use when this capability is needed.
metadata:
  author: kimhons
---

## Ralph Ultra Autonomous Loop

Start the smart adaptive development loop that iterates through PRD stories.

### What this does

1. **Pre-flight checks** — Runs environment-doctor and deep-codebase-navigator
2. **Story selection** — Picks next story respecting dependencies and failure history
3. **Prompt construction** — Builds context from CLAUDE.md, skills, progress, and error history
4. **Execution** — Invokes Claude/AMP with appropriate security flags
5. **Verification** — Runs post-iteration skills (security-auditor, performance-guardian)
6. **Failure handling** — Circuit breaker at 3 failures, error injection for self-correction
7. **Checkpointing** — Saves state after each iteration for resumability

### Usage

```
/ralph-ultra:ralph-run [--max-iterations N] [--security sandbox|standard|yolo] [--tool claude|amp] [--resume]
```

### Options

| Option | Default | Description |
|--------|---------|-------------|
| `--max-iterations` | 10 | Maximum loop iterations |
| `--security` | standard | Security mode (sandbox/standard/yolo) |
| `--tool` | claude | AI tool to use |
| `--resume` | false | Resume from last checkpoint |

### Loop Flow

```
Pre-flight Skills → Pick Story → Build Prompt → Execute →
  ↓ Success: Mark done, run post-iteration skills → Next story
  ↓ Failure: Record error, inject context → Retry (max 3) → Block story
```

### Security Modes

- **sandbox**: Read-only analysis, no file modifications
- **standard**: Allowlist-based, only permitted commands
- **yolo**: Full permissions (requires explicit confirmation)

### Monitoring

While the loop runs:
- View live dashboard: `/ralph-ultra:ralph-dashboard`
- Check status: `/ralph-ultra:ralph-status`
- Session logs: `.ralph-ultra/sessions/current/log.jsonl`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kimhons) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

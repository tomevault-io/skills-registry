---
name: oracle
description: | Use when this capability is needed.
metadata:
  author: richardgill
---

# Oracle

Escalate to GPT-5.4 with high reasoning effort for problems requiring deeper analysis. Use for:
- Complex debugging with elusive bugs
- Architectural decisions and tradeoffs
- Security audits and vulnerability analysis
- Code review requiring deep reasoning

## Main command

Run Codex with GPT-5.4 in non-interactive mode with high reasoning:

```bash
codex exec -m gpt-5.4 --sandbox read-only -c model_reasoning_effort='"high"' "$PROMPT"
```

Where `$PROMPT` is your analysis request. Codex bundles relevant context from your repo automatically.

## Reasoning effort

- `high` — deep reasoning, ~3x tokens, best for complex problems (oracle default)
- `medium` — balanced (codex default)
- `low` — fast, minimal thinking

## Options

- `--sandbox read-only` — analyze without modifying files (recommended)
- `--sandbox workspace-write` — allow file modifications if needed
- `-o /tmp/oracle-response.md` — save response to file
- `-C <dir>` — run in specific directory

## Guidelines

- Oracle is **read-only** — analyzes but doesn't modify code
- Treat outputs as advisory — verify against codebase
- Use for genuinely complex problems (not routine tasks)
- High reasoning uses ~3x tokens but thinks deeper

## Examples

```bash
# Debug intermittent auth issue
codex exec -m gpt-5.4 --sandbox read-only -c model_reasoning_effort='"high"' \
  "The auth flow fails intermittently. Check src/auth/ for race conditions."

# Architectural review
codex exec -m gpt-5.4 --sandbox read-only -c model_reasoning_effort='"high"' \
  "Review src/api/ data flow and suggest improvements for scalability."

# Security audit
codex exec -m gpt-5.4 --sandbox read-only -c model_reasoning_effort='"high"' \
  "Audit src/handlers/ for OWASP top 10 vulnerabilities."
```

## What to ask oracle:
$ARGUMENTS

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/richardgill) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

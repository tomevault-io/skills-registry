---
name: consult
description: Get a second opinion from another AI: Codex or another Claude model. Read-only. Use when this capability is needed.
metadata:
  author: soliblue
---

# Consult

Get a second opinion from another AI.

## Routing

| Hint | Engine | Command |
|------|--------|---------|
| `codex`, `openai`, `gpt` | Codex | `codex exec -s read-only` |
| `haiku` | Claude Haiku | `claude -p --model haiku` |
| `sonnet` | Claude Sonnet | `claude -p --model sonnet` |
| `opus` | Claude Opus | `claude -p --model opus` |
| *(no hint)* | Claude Sonnet | `claude -p --model sonnet` |

## Commands

```bash
claude -p --model <model> --tools "Read,Glob,Grep,WebFetch,WebSearch" --dangerously-skip-permissions "QUESTION"
```

```bash
codex exec -s read-only -C "$(git rev-parse --show-toplevel)" "QUESTION"
```

## Workflow

1. Take the question from the `/consult` arguments.
2. Detect the engine from the routing table.
3. Strip the model hint before passing the question.
4. Run the command with a 5 minute timeout.
5. Present the answer and clearly label the model.
6. Add your own view only if it improves the result.

## Notes

- All engines are read-only.
- Good for sanity checks, alternative approaches, blind spots, and architecture reviews.
- Default is Sonnet.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/soliblue) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

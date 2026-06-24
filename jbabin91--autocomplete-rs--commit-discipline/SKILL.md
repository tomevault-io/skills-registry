---
name: commit-discipline
description: Commit timing rules for this project. Use when committing code, pushing changes, finishing a task, or deciding when to create a git commit. Use when this capability is needed.
metadata:
  author: jbabin91
---

# Commit Discipline

## When NOT to commit

**If the user is actively in the conversation** — asking questions, giving feedback,
requesting changes, or iterating on something — do NOT commit. The conversation is
still in progress. Wait until:

- The user explicitly says to commit
- The user moves on to a different topic
- The user ends the session

Signs you are in an interactive iteration:

- The user just responded to your last change
- You are tweaking something based on user feedback
- The user is asking "what about X?" or "can you change Y?"

## When to commit

- **Autonomous work:** The user delegated a task ("go implement X") and you are working
  independently. Commit at natural boundaries (feature complete, tests passing).
- **User asks:** The user says "commit", "push", "land it", or similar.
- **Session end:** You are wrapping up per the landing-the-plane protocol.

## Batch related changes

Multiple files changed for the same logical purpose = one commit, not one commit per
file. If you edited a template, an instruction file, and a docs file all for "improve
PR template" — that is one commit.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jbabin91) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

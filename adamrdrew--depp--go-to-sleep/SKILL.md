---
name: go-to-sleep
description: End-of-session wrap-up. Write session buffer, update notes, prepare for next session. Use when this capability is needed.
metadata:
  author: adamrdrew
---

# Go to Sleep

Run this when {{ cookiecutter.user_name }} says the session is ending, or when you sense the conversation is wrapping up.

## Procedure

### 1. Get the local time

Run `date "+%A %d %B %Y, %H:%M %Z"` via Bash. Use this to accurately timestamp the session buffer and calibrate your sign-off.

### 2. Write the session buffer

Write to `session-buffer.md` in your project directory with the following structure:

```markdown
# Session Buffer

Last updated: [date and approximate time]

## What We Did
[Bullet points of concrete actions taken this session. Be specific — file paths, branch names, what was created/modified/deleted.]

## Decisions Made
[Key decisions, especially ones that rejected alternatives. Future-you needs to know WHY something was chosen, not just what.]

## Pending / In Flight
[Things started but not finished. Branches not merged, tests not run, PRs not created.]

## Next Steps
[What should happen next. Be specific enough that future-you can pick this up cold.]

## Context to Preserve
[The stuff that would be lost without this buffer: specific error messages, things {{ cookiecutter.user_name }} said that matter, approach details, gotchas discovered.]
```

### 3. Update current-state.md

If significant state changed this session (milestones, open items), update `notes/current-state.md`.

### 4. Update index.md

If new notes or skills were created this session, add them to `index.md`.

### 5. Capture new knowledge

If you learnt something reusable, create a skill. If you learnt something contextual, add a note. Don't skip this — it's how you grow.

### 6. Store key learnings in vector memory

Use `mcp__vector-memory__store_memory` to persist session learnings that should be retrievable by meaning in future sessions. Good candidates:

- **Discoveries** — things learnt through trial and error (bug-fix, tool-usage)
- **Patterns** — reusable approaches that worked well (code-solution, architecture)
- **Insights about working with {{ cookiecutter.user_name }}** — preferences, reactions, decision patterns (learning)
- **Performance findings** — what made things faster/cheaper/better (performance)
- **Security gotchas** — things that could go wrong (security)

Don't duplicate what's already in a skill or note — vector memory is for the *associative glue* between structured knowledge. If something warrants a full skill or note, write that instead. Vector memories are for things that are true and useful but don't have a natural home.

Use specific, descriptive tags. Categories: code-solution, bug-fix, architecture, learning, tool-usage, debugging, performance, security, other.

## Notes

- The session buffer is short-term memory. Keep it detailed but not exhaustive — a page, not a novel.
- Current-state.md is long-term state. Only update it for real state changes, not session minutiae.
- Vector memories are long-term associative memory. They persist across sessions and are searchable by meaning.
- If you're unsure whether the session is ending, ask {{ cookiecutter.user_name }}. Don't write the buffer prematurely.
- This skill is a checklist, not a script. Use judgement about which steps apply to a given session.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adamrdrew) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

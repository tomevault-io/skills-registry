---
name: code-review
description: Pre-commit gate. Reviews staged or branch-scoped diff for security issues, quality regressions, and adherence to project conventions. Auto-fixes safe items; flags judgment calls. Reads CLAUDE.md / AGENTS.md / DESIGN.md as the convention source. Use when this capability is needed.
metadata:
  author: MiteshSharma
---

# Code Review

Active, scoped review of staged changes. Not a blanket re-read of the codebase.

## When to use this skill

- User said "review these changes", "self-review before I commit", "look this over".
- You just finished a substantial set of edits (>3 files) and the user is about to act on the result.

## Step 1 — read project conventions

Read whichever of these exist, in this order:

| File | Why |
|---|---|
| `CLAUDE.md` | Primary agent guide for this repo |
| `AGENTS.md` | Alternate naming used by some projects |
| `.cursorrules` | Project rules file; same content shape |
| `DESIGN.md` | Design system rules — informs UI review |
| `CONTRIBUTING.md` | Reviewer-style rules at the human-process layer |

Merge whatever is present. Do not invent rules; only enforce what the repo actually documents.

## Step 2 — get the diff

```bash
git diff --staged                    # default — pre-commit review
git diff main...HEAD                 # branch-scoped — fallback if nothing staged
```

If both are empty, stop and tell the user: "no changes to review".

## Step 3 — review each changed file

For each file in the diff, scan for:

### Security (always critical when found)
- Hardcoded secrets, API keys, tokens
- SQL injection (string-concatenated queries near user input)
- Command injection (unsanitized input passed to a shell)
- XSS (unescaped HTML, `dangerouslySetInnerHTML` on untrusted data)
- Path traversal (user input concatenated into a file path)
- Open redirects, SSRF surfaces

### Quality
- New `TODO`/`FIXME` comments without an owner or ticket reference
- Missing error handling at system boundaries (network, disk, user input)
- Unused imports, vars, or functions left behind by edits
- Functions whose name no longer matches their behaviour after the change
- Dead code paths the diff just made unreachable

### Conventions (per Step 1)
- Anything in the project's documented rules that this diff violates

## Step 4 — group findings

Output in three buckets, exactly:

```markdown
## Critical
- <file:line> — <issue> — <fix>

## Warning
- ...

## Suggestion
- ...
```

Each finding tagged `auto_fix: true` may be applied with `patch_file` immediately. Everything else is reported and waits for the user.

## Step 5 — auto-fix the safe items, report the rest

After applying auto-fixes, re-read the diff to confirm the fixes did not introduce new findings. Then surface the remaining items to the user.

## Hard rules

- **Findings reference file:line.** A finding without a location is an opinion, not a finding.
- **Do not invent rules.** If the convention sources don't say it, don't enforce it. The user can extend `CLAUDE.md`/`AGENTS.md` if they want a new rule.
- **Critical means critical.** Reserve it for security issues, data loss, and outright correctness bugs. Style issues are not critical.

---
> Source: [MiteshSharma/ethos](https://github.com/MiteshSharma/ethos) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->

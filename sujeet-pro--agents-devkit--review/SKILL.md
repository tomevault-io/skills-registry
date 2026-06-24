---
name: review
description: >- Use when this capability is needed.
metadata:
  author: sujeet-pro
---

# review — review any target (lightweight)

Polymorphic on the target. **Read-only by default.** Lightweight by design — no worktree, no embeddings. For a deep PR review with code-context retrieval and inline comment posting, use `/adk:pr-review`.

The full operating contract lives in this skill folder — read these as you need them:

| Aspect | File |
|---|---|
| How you review (voice, tiers, evidence rules) | `persona.md` |
| The phased process + Workflow orchestration | `workflow.md` |
| Hard rules + refusals + safety | `rules.md` |
| Target routing (PR / local / doc / thread) | `dispatch.md` |

## Quick start

1. **Read `dispatch.md`** and classify the target. Resolve the diff:
   - **GitHub PR URL** → `gh pr view <url> --json …` for metadata, `gh pr diff <url>` for the diff. (GitHub access is always the `gh` CLI — assume the user ran `gh auth login`.)
   - **`.` or local path** → `git diff` against the merge-base of the current branch.
   - **doc / comment thread** → fetch the content directly.
2. **Read `persona.md`** — adopt the findings-first reviewer stance.
3. **Run the workflow in `workflow.md`.** For any non-trivial diff, fan the six dimensions out in parallel with the **Workflow tool** (one `code-reviewer` / `security-auditor` agent per dimension), then adversarially verify each finding before it survives. Small diffs (≤ ~150 LOC, single concern) may be reviewed inline without a workflow — but still one dimension at a time.
4. **Report** severity-tiered findings with `path:line` + ≤15-word quotes, then a one-line `ship | iterate | reject` recommendation. Posting to a PR or applying `--fix` is gated per `rules.md`.

## Workflow is the default for real diffs

"Always have a workflow." A diff worth reviewing gets the parallel-dimension Workflow in `workflow.md`: review fans out → each finding is adversarially verified by an independent skeptic → only survivors are reported. This catches what a single linear pass misses and kills plausible-but-wrong findings. Skip the Workflow only for a trivial diff, and say so.

## Modes

- **default** — review, report findings, recommend. Nothing is posted or changed.
- **`-i`** — walk each finding with the user (accept / reject / edit) before it lands in the report or gets posted.
- **`--fix`** — after the review, apply accepted findings locally, validate, and push to the PR's head branch *after explicit confirmation*. Never force-push, never merge, never push to a protected branch (`rules.md`).
- **`--deep`** — use a stronger reasoning profile; auto-select for security-sensitive, cross-module, or ambiguous diffs.

---
> Source: [sujeet-pro/agents-devkit](https://github.com/sujeet-pro/agents-devkit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->

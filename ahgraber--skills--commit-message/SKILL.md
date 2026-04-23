---
name: commit-message
description: |- Use when this capability is needed.
metadata:
  author: ahgraber
---

# Commit Message

Draft a Conventional Commit message from staged changes.

## Invocation Notice

- Inform the user when this skill is being invoked by name: `commit-message`.

## Critical Constraints

- Use only staged changes (`--cached` / staged SCM state) as input.
- Resolve and use the repository root for all SCM calls.
- If no staged changes exist after one retry, stop and inform the user.
- Always include an `AI-assistant: <AGENT>` footer.
- Output only the final commit message in one markdown code block.

## Workflow

1. Resolve repository root.
   Prefer workspace root when known; otherwise run `git rev-parse --show-toplevel`.
2. Retrieve staged changes.
   Prefer `get_changed_files` with `repositoryPath: <repo-root>` and staged state.
   If that tool is unavailable, use SCM tooling with explicit repo context.
   If SCM tooling is unavailable, use `git -C <repo-root> diff --cached`.
3. Validate staged content exists.
   If empty, retry once with explicit repo root; if still empty, inform user and stop.
4. Analyze the staged diff.
   Identify changed files, behavior impact, logical scope, and likely commit type.
5. Incorporate user arguments/context when provided.
   Preserve explicit issue refs and constraints from user input.
6. Draft the commit message using `references/conventional-commit-rules.md`.
7. Return only the final message in a fenced code block, ready for `git commit -F -`.

## References

- `references/conventional-commit-rules.md` - subject/body/footer rules and examples.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ahgraber) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

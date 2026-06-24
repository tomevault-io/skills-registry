---
name: workspace-digest
description: Summarize the files saved in this assistant's shared workspace. Use when the user asks what is in their workspace, for a file inventory, or a digest of saved work. Use when this capability is needed.
metadata:
  author: cloudflare
---

# Workspace Digest

Produce a concise inventory of the files saved in this assistant's shared
workspace (the same workspace every chat under this user shares).

## Process

1. Run `scripts/digest.ts` to scan the workspace and build a file inventory.
   Pass `{ "dir": "/some/path" }` to scope the scan to a subdirectory.
2. Summarize the result for the user: how many files, the notable ones, and
   the total size.
3. Offer to open or read any specific file the user is interested in.

The TypeScript script is function-style (`export default run(input, ctx)`). It
reads the workspace through `ctx.workspace` (read-only) and a formatting hint
bundled alongside the skill via `ctx.files`.

## Output format

Keep the digest short - a markdown list of files with sizes, plus a one-line
summary. Only print the full listing if the user asks for it.

---
> Source: [cloudflare/agents](https://github.com/cloudflare/agents) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->

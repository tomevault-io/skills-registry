---
name: gh-pr-to-green
description: - Get context from PR with `gh`, make sure if have all comments Use when this capability is needed.
metadata:
  author: transloadit
---

# GH PR to Green

## Workflow

- Get context from PR with `gh`, make sure if have all comments
- Write them as a markdown todolist in `repodocs/prompts/${YYYY}-${MM}-${DD}-${sluggedSemanticTopic}.md` and commit it. If the `repodocs/prompts/` dir does not exist, use the `docs/prompts/` dir.
- Merge the latest main into our branch and resolve any conflict carefully.
- Determine which review items are correct.
- Keep working until all items are checked off, either by fixing the issue, or explaining why not in the markdown list as well as comments in code where applicable/sensible
- commit and push
- Invoke `council-review`
- Run the repo-required checks (often `yarn check`)
- Fix any issue that you deem related and worth fixing
- commit and push
- Monitor CI until green:
  - Prefer `gh run watch` (works everywhere with `gh`).
  - If `gh-run-watch.ts` exists in the repo, it's fine to use that too.
- Fix any issue that you deem related and worth fixing
- Rinse and repeat until CI is green, or there are only 100% unrelated issues remaining

Report back with a list of all changes made, and offer a link to the PR for inspection and merge. Offer to squash merge with `--admin` if the human thinks a last manual review is not needed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/transloadit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

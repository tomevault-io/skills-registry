---
name: changeset
description: >- Use when this capability is needed.
metadata:
  author: lifinance
---

# Authoring a changeset

This repo releases with **Changesets**: every PR that changes a publishable package
carries a small `.changeset/*.md` file declaring which packages bump and by how much.
`changeset version` later consumes those files into per-package `CHANGELOG.md`s and
version bumps. No changeset → no release for that change; `changeset-bot` comments a
reminder on the PR (a nudge, not a hard block — the maintainer-reviewed Version PR is the
real gate). Your job here is to write a correct changeset for the work in progress.

## Steps

1. **See what changed.** Fetch and diff against the base branch:
   ```bash
   git fetch origin main
   git diff --name-only origin/main...HEAD
   ```
   (Use the working tree too if changes aren't committed yet: `git status`.)

2. **Map files → packages.** A file under `packages/<dir>/` belongs to that package.
   Read `references/bump-rules.md` for this repo's **publishable** vs **private/ignored**
   package list and the dependency graph. Only publishable packages need a changeset.

3. **Decide the bump per package.** On this `0.x` line: `fix:`/`feat:` → **patch**, a
   breaking change → **minor** (a stable `1.0.0` is a separate, deliberate cut). See
   `references/bump-rules.md` for the full rationale (and why you should *not* list
   cascade-only dependents).

4. **Write the file.** Create `.changeset/<short-kebab-name>.md` in the exact frontmatter
   format from `references/format.md`. The summary becomes the changelog line, so write it
   for a reader of the release notes, not a commit log — **1–2 lines max, short but
   descriptive**.

5. **Confirm.** Run `pnpm changeset status` to verify Changesets sees your file and the
   intended packages bump (including the automatic dependent cascade).

6. **Commit and push it.** A changeset only counts once it's on the PR — don't leave it as
   a loose working-tree file. Commit and push to the current branch:
   ```bash
   git add .changeset/*.md
   git commit -m "chore: add changeset"
   git push   # no upstream yet? git push -u origin HEAD
   ```
   Pushing to the PR's head branch updates the open PR automatically.

## Key rules (full detail in `references/`)

- **Only declare packages you intentionally changed.** Internal dependents re-release
  automatically (`updateInternalDependencies: patch`); authoring changesets for them
  double-counts and produces noisy changelogs.
- **Skip** docs-only, chore-only, test-only, and private-package-only changes. For a
  deliberately release-less change, `pnpm changeset --empty`.
- One changeset can cover multiple packages; use multiple changesets if different parts of
  the work deserve different changelog entries.

---
> Source: [lifinance/bigmi](https://github.com/lifinance/bigmi) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-17 -->

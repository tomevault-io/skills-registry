---
name: pre-release
description: Run a series of pre-release checks on the repo. Use when this capability is needed.
metadata:
  author: mattpocock
---

Check all of the files in the `.changeset` directory.

For the changesets as a whole, check the following:

- Each changeset should be about a user-facing feature. Internal refactors or things that don't affect users should not be added as changesets.
- Related changes should be grouped under a single changeset.
- Changesets are user-facing, not maintainer-facing. They should not specify implementation details, or how a feature was made. They should only specify the user-facing change, and why it was made. Changesets !== commit messages.

Once done, commit your changes.

---
> Source: [mattpocock/sandcastle](https://github.com/mattpocock/sandcastle) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-25 -->

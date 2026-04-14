---
name: bazel
description: Uses Bazel to run builds, tests, linting and format Use when this capability is needed.
metadata:
  author: checkmk
---

# Repo-specific flags

```bash
# Edition (affects which cmk targets are built/tested)
--cmk_edition=community|pro|ultimate|ultimatemt|cloud

# Configs defined in .bazelrc
--config=mypy      # type checking
--config=clippy    # Rust linting

# CI excludes these to avoid xunit parser issues (fine to include locally)
-//packages/livestatus/... -//packages/neb/... -//packages/unixcat/...
```

# formatting

```bash
# to fix
bazel run //:format  [paths...]
# just check
bazel run //:format.check [paths...]
```

You should specify the full path of the folders/files to format.
You can use `git show --name-only --pretty=` to target formatting only to changed files.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/checkmk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

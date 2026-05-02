---
name: resolve-dep-paths
description: Resolves `<dep>/path` references to local filesystem paths. Use this whenever you encounter a `<dep>/path` reference in documentation and need to read the file. Use when this capability is needed.
metadata:
  author: powdr-labs
---

# Resolve Dependency Paths

When you encounter a `<dep>/<path>` reference (e.g. `<openvm>/crates/vm/src/arch/integration_api.rs`), resolve it by running the script in this skill's directory:

```bash
# List all dependency roots:
.claude/skills/resolve-dep-paths/resolve.sh

# Get a single dependency root:
.claude/skills/resolve-dep-paths/resolve.sh openvm
```

Then use the Read tool with the resolved path. For example, if the script prints `/some/absolute/path/.cargo/git/checkouts/openvm-HASH/REV`, read `<openvm>/crates/vm/src/arch/integration_api.rs` as `/some/absolute/path/.cargo/git/checkouts/openvm-HASH/REV/crates/vm/src/arch/integration_api.rs`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/powdr-labs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

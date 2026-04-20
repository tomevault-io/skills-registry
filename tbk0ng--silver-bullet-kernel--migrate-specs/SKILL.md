---
name: migrate-specs
description: Sync OpenSpec delta specs into canonical specs with explicit dry-run/apply flow. Use when this capability is needed.
metadata:
  author: tbk0ng
---

# Migrate Specs

Use this skill to synchronize `openspec/changes/<change>/specs/*/spec.md` into `openspec/specs/*/spec.md` without archiving the change.

## Usage

```text
$migrate-specs
```

## Execution Flow

1. Resolve target change (`openspec list --json` if needed).
2. Preview migration:

```bash
npm run sbk -- migrate-specs --change <change-id>
```

3. Apply migration:

```bash
npm run sbk -- migrate-specs --change <change-id> --apply
```

4. Validate:

```bash
openspec validate --all --strict --no-interactive
```

5. Update `openspec/changes/<change>/tasks.md` evidence rows with migration outcomes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tbk0ng) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

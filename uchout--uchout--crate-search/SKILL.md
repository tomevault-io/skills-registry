---
name: rs-crate-search
description: Search and evaluate Rust crates for a problem. Presents options for user approval. Use when this capability is needed.
metadata:
  author: uchout
---

# /rs-crate-search

Find and evaluate crates for a problem domain.

## Usage

```
/rs-crate-search "json parsing"
/rs-crate-search "http client async"
```

## Workflow

### 1. Search

```bash
# Search crates.io
cargo search "query" --limit 10

# Check lib.rs for alternatives
# WebSearch: "rust crate for [problem] site:lib.rs"
```

### 2. Evaluate Each Candidate

| Criteria | Check |
|----------|-------|
| Maintenance | Last commit < 6 months |
| Adoption | Downloads, GitHub stars |
| Dependencies | `cargo tree -p crate` |
| API quality | Docs, examples |

### 3. Present to User

```markdown
## Crates for [problem]

| Crate | Downloads | Updated | Deps |
|-------|-----------|---------|------|
| `foo` | 10M/month | 2025-01 | 3 |
| `bar` | 500K/month | 2024-12 | 12 |

### `foo`
- Pros: Fast, minimal deps, good docs
- Cons: Less flexible API

### `bar`
- Pros: Feature-rich, active community
- Cons: Heavy dependency tree

**Recommendation:** `foo` - balances simplicity and capability

Add `foo` to Cargo.toml?
```

### 4. Wait for User Approval

Never add dependency without explicit user confirmation.

## Quick Evaluation

For known-good crates, check version only:

```bash
cargo search serde --limit 1
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/uchout) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

---
name: tdd-guide
description: vive-specific TDD guidance (locations and commands). Use when this capability is needed.
metadata:
  author: k4h4shi
---

# TDD Assistant (vive)

Nested TDD を vive のテスト構成に合わせて実行する。

## Preparation

- `docs/requirements.md` と `docs/architecture.md`、Issue を読む
- worktree/タスクのディレクトリにいることを確認

## Outer Loop (Integration/Behavior)

- 対象: `tests/` または高レベルのユニットテスト
- 実行: `cargo test`

## Inner Loop (Unit)

- 対象: `src/` の `#[test]`
- 実行: `cargo test`

## Finalization

- `docs/` の更新

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/k4h4shi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

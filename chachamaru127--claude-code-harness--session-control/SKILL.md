---
name: session-control
description: /work のセッション resume/fork(branch) を --resume/--fork フラグに基づいて制御。session.json と session.events.jsonl を更新する内部ワークフロー専用スキル。Do NOT load for: user session management, login state, app state handling. Use when this capability is needed.
metadata:
  author: chachamaru127
---

# Session Control Skill

/work の `--resume` / `--fork` フラグに応じてセッション状態を切り替える。

## 機能詳細

| 機能 | 詳細 |
|------|------|
| **セッション再開/分岐** | See [references/session-control.md](${CLAUDE_SKILL_DIR}/references/session-control.md) |

## 実行手順

1. workflow から渡された変数を確認
2. `scripts/session-control.sh` を適切な引数で実行
3. `session.json` と `session.events.jsonl` の更新を確認

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chachamaru127) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

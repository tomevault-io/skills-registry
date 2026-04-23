---
name: implementation-rules
description: Lightweight implementation workflow. Use for code changes, fixes, refactors, and feature work. Read repo rules first and pull in deeper references only when needed. Use when this capability is needed.
metadata:
  author: ryoyayahagi
---

# Implementation Rules

## Quick start

- `rules.md` があれば先に読む
- 既存パターンに沿って最小差分で直す
- 必要なテスト、ビルド、lint、型チェックを実行する
- git操作を含む依頼は `git-ops` を使う

## 必須ルール

- 公開挙動を変える変更は明示する
- ダミー実装や未解決TODOを残さない
- 失敗を再現できるテストを優先する
- iOS実装で `*.xcodeproj` か `*.xcworkspace` がある場合は、ビルド成功後に `appium-simulator-test` で差分機能を確認する
- テストで直せない不具合や追加要求を見つけたら Issue 化する

## 参照の深さ

- 通常はこのファイルと repo の `rules.md` で進める
- 詳細な報告形式や例が必要なときだけ `references/implementation-rules.md` を参照する

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ryoyayahagi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

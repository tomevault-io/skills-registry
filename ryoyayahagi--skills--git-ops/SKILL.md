---
name: git-ops
description: Minimal git workflow for branch setup, commit, push, and PR creation when git work is explicitly requested or code changes are complete. Use when this capability is needed.
metadata:
  author: ryoyayahagi
---

# Git Ops

## 自動トリガー

- コミット、push、PR、merge、deploy を求められたとき
- 実装が一区切りつき、コミットが必要なとき

## 必須ルール

- main/master/develop へ直接実装しない
- 1機能 1ブランチ、1機能 1コミットを守る
- 作業前か切替前に `gh issue list --state open --limit 20` を確認できるなら確認する
- コミット前に `git status` と関連テスト結果を確認する
- PR作成までは自動でよいが、マージは人間レビュー後

## 参照の深さ

- 通常はこのファイルだけで進める
- 命名例や詳細運用が必要なときだけ `references/git-policy.md` を参照する

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ryoyayahagi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

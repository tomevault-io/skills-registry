---
name: commit-push-pr-flow
description: | Use when this capability is needed.
metadata:
  author: acta0724
---

Commit, push, and open a pr

## 手順

### 1. verification-loop
verification-loop を実行し、全て PASS であることを確認すること。
ただし auto-execute フロー���ら呼ばれ、既に verification-loop が PASS 済みの場合はスキップしてよい。

### 2. process-commit
process-commit を実行し、変更を論理的な単位に分割してコミットする。
ただし以下の場合はスキップしてよい:
- 変更ファイルが 1-2 個で、全て同一目的の変更である場合
- 既にコミット済みで未コミットの変更がない場合

### 3. push & PR
- create branch(if current branch in default) and pr
- following pr template
- description in japanese
- 提出後は gh pr view --web で差分を共有して完了してください

### 4. review-flow
完了後 review-flow を呼び出します。
ただし auto-execute フローから呼ばれている場��はスキップ（Phase 6 で別途実行される）。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/acta0724) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

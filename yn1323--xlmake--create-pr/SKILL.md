---
name: create-pr
description: GitHubにPull Requestを作成するスキル。コミット・プッシュ済みの状態で、現在のブランチの変更内容を分析し、適切なタイトルと説明文でPRを作成する。「PRを作成して」「プルリクエストを作って」「PR作って」などのトリガーで発動。 Use when this capability is needed.
metadata:
  author: yn1323
---

# PR作成スキル

## 前提条件

- コミット済み
- プッシュ済み
- GitHubリポジトリと連携済み

## ワークフロー

### 1. 現在のブランチ情報を取得

```bash
git branch --show-current
```

### 2. デフォルトブランチを自動検出

```bash
gh repo view --json defaultBranchRef --jq '.defaultBranchRef.name'
```

### 3. 変更内容を把握

デフォルトブランチとの差分コミットを取得:

```bash
git log <default-branch>..HEAD --oneline
```

詳細な変更内容を確認:

```bash
git diff <default-branch>...HEAD --stat
```

### 4. PRを作成

```bash
gh pr create --title "<タイトル>" --body "<本文>"
```

## PR本文フォーマット（英語で出力）

```markdown
## Summary
- 変更内容の要約を箇条書きで（1-3点）

## Changes
- 具体的な変更点を箇条書きで

## Test Plan
- テスト方法や確認事項
```

## 注意事項

- PRのタイトルと本文は**英語**で作成すること
- コミットメッセージを参考にしつつ、読み手にわかりやすい表現にする
- 変更が大きい場合はChangesセクションを充実させる
- 変更が小さい場合はシンプルに保つ

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yn1323) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

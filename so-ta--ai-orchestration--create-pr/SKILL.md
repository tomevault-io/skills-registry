---
name: create-pr
description: | Use when this capability is needed.
metadata:
  author: so-ta
---

# Create Pull Request

自己レビュー完了後のPR作成。

## 前提条件

- self-review が完了している
- テストがすべてパスしている
- 必要なドキュメント更新が完了している

## 手順

### 1. 最終確認

```bash
# 変更内容の確認
git status
git diff --stat

# テスト最終確認
cd backend && go test ./...
cd frontend && npm run check
```

### 2. コミット

```bash
git add .
git commit -m "<type>: <description>"
```

コミットメッセージ形式:
- `feat:` 新機能
- `fix:` バグ修正
- `refactor:` リファクタリング
- `docs:` ドキュメント
- `test:` テスト
- `chore:` その他

### 3. プッシュ

```bash
git push -u origin <branch-name>
```

### 4. PR作成

```bash
gh pr create --title "<type>: <description>" --body "## Summary

- 変更内容の要約

## Changes

- 具体的な変更点

## Test Plan

- テスト方法"
```

## PR作成後

PR作成後は review-pr スキルでCIとCodexレビューの結果を確認する。

## 注意事項

- ローカルCIが通っていない状態でpushしない
- コミットメッセージは明確で簡潔に
- PRの説明は変更の「なぜ」を含める

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/so-ta) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

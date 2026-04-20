---
name: pr-review-extraction
description: Extract and summarize review comments from GitHub PRs. Use when analyzing PR reviews, checking unresolved issues, or responding to CodeRabbit feedback. Use when this capability is needed.
metadata:
  author: endo-ava
---

# PRレビューコメント抽出

レビューコメントを効率的に抽出し、対応すべき項目をチェックリスト化します。

## 主な機能

- **Resolved除外**: Resolved状態のコメントを自動除外（未解決のみ表示）
- **統計表示**: 未解決/全体のコメント数を表示
- **GitHub連携**: 各コメントへの直接リンク付き
- **チェックリスト**: 対応状況を追跡可能

## 使用方法

### 基本的な使い方

```bash
# 未解決のレビューコメントを取得（デフォルト: 300文字まで表示）
python3 .claude/skills/pr-review-extraction/extract_reviews.py <PR_NUMBER>
```

### オプション

```bash
# 完全なコメント表示（切り詰めなし）
python3 .claude/skills/pr-review-extraction/extract_reviews.py <PR_NUMBER> --full
```

## 実行例

```bash
# PR #7のレビューを取得（未解決のみ、300文字まで）
python3 .claude/skills/pr-review-extraction/extract_reviews.py 7

# PR #7のレビューを取得（完全表示）
python3 .claude/skills/pr-review-extraction/extract_reviews.py 7 --full
```

## 出力例

```markdown
# Review Report (PR #7)

## 🚨 Code Suggestions (Inline)

- [ ] **ingest/collectors/spotify.py:42**
  - 指摘: Consider using async context manager for better resource handling...
  - [View on GitHub](https://github.com/...)

## 📝 Summary & Walkthrough

- [ ] **PR Summary / Report** ([View on GitHub](https://github.com/...))
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/endo-ava) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

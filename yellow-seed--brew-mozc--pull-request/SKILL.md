---
name: pull-request
description: Pull Request作成スキル。.github/PULL_REQUEST_TEMPLATE.mdに準拠した高品質なPRを作成。Use when: PR作成、Pull Request作成、変更内容のPR化を依頼された時。 Use when this capability is needed.
metadata:
  author: yellow-seed
---

# Pull Request作成

`.github/PULL_REQUEST_TEMPLATE.md`のテンプレート形式に準拠した高品質なPull Requestを作成します。

## PR作成原則

1. **コミット履歴の分析**: ブランチのコミット履歴から変更内容を把握
2. **What/Why/Howの明確化**: 何を、なぜ、どのように変更したかを明示
3. **レビュアー視点**: レビュアーが理解しやすい構成と説明
4. **テンプレート準拠**: 既存のPRテンプレート形式に従う
5. **Issue連携**: 関連Issueを正確に参照

## PR概要の構成要素

| 要素 | 必須/任意 | 説明 |
| ----- | ------------- | ------------- |
| Summary | 必須 | PRの目的と背景を2-3文で説明 |
| Type of Change | 必須 | 変更種別を選択 |
| Related Issues | 必須 | 関連Issue番号を記載 |
| Changes | 必須 | 変更内容を詳細に列挙 |
| Test Plan | 必須 | テスト方法と確認事項 |
| Screenshots | 任意 | UI変更時のスクリーンショット |
| Additional Notes | 任意 | その他の注意事項 |

## What/Why/How の明確化

| 要素 | 記述場所 | 説明 |
|------|----------|------|
| **What（何を）** | Summary, Changes | 何を変更したか |
| **Why（なぜ）** | Summary | なぜ変更したか（目的、背景、効果） |
| **How（どのように）** | Changes | どのように変更したか（実装方法、詳細） |

## PR作成手順

### 1. ブランチとコミット履歴を確認

```bash
# 現在のブランチを確認
git branch --show-current

# ベースブランチとの差分コミットを確認
git log main...HEAD --oneline

# 変更ファイルを確認
git diff main...HEAD --name-only

# 変更内容の詳細を確認
git diff main...HEAD
```

### 2. Summary を生成

**目的**: PRの「何を」「なぜ」「効果」を2-3文で説明

**生成方法**:
1. コミットメッセージのtype（feat, fix, docsなど）を確認
2. 複数のコミットから共通の目的を抽出
3. Issueの説明文を参考に背景を記述
4. 2-3文で簡潔にまとめる

**例**:
```markdown
## Summary

シェルスクリプトの品質を保証するため、shellcheck と shfmt を使用したリンティング環境を構築します。これにより、バグの早期発見とコードスタイルの統一が可能になります。
```

### 3. Type of Change を選択

変更の性質に応じて適切な種別を選択:

- Bug fix (non-breaking change which fixes an issue)
- New feature (non-breaking change which adds functionality)
- Breaking change (fix or feature that would cause existing functionality to not work as expected)
- Documentation update
- Refactoring (no functional changes)
- Performance improvement
- Test update

### 4. Related Issues を記載

Issue番号を正確に記載:

- `Closes #XXX`: このPRで解決するIssue（マージ時に自動クローズ）
- `Fixes #XXX`: バグ修正の場合
- `Related to #YYY`: 関連するが自動クローズしないIssue

**例**:
```markdown
## Related Issues

Closes #20
Related to #15
```

### 5. Changes を構成

**目的**: 「何を」「どのように」変更したかを詳細に説明

**生成方法**:
1. `git diff --name-only` でファイル一覧を取得
2. 関連ファイルをグループ化（例: GitHub Actions, Docker, Docs）
3. 各グループで変更内容を階層的に記述

**例**:
```markdown
## Changes

- GitHub Actions ワークフロー (.github/workflows/shell-linting.yml) を追加
  - PR と main ブランチへのプッシュで自動実行
  - shellcheck による静的解析
  - shfmt によるフォーマットチェック

- Docker 環境を追加
  - Dockerfile.shell-linting: shellcheck と shfmt をインストール
  - docker-compose.shell-linting.yml: 簡単に実行可能な設定

- ドキュメントを追加
  - .claude/skills/shell-linting/SKILL.md: 詳細なルールとベストプラクティス
  - docs/shell-linting.md: ユーザー向けガイド
```

### 6. Test Plan を生成

**目的**: テスト方法と確認事項を明確にする

**生成方法**:
1. Issueの受け入れ基準をTest Planに変換
2. テストコミットからテスト項目を抽出
3. チェックリスト形式で記述（実施済みは`[x]`、未実施は`[ ]`）

**例**:
```markdown
## Test Plan

- [x] shellcheck が全スクリプトでエラーなく実行される
- [x] shfmt のフォーマットチェックが通る
- [ ] GitHub Actions ワークフローが正常に動作する
- [ ] Docker 環境でリンティングが実行できる
```

### 7. Checklist を確認

PR提出前に以下を確認:

- コードはプロジェクトのスタイルガイドに従っている
- 自己レビューを実施した
- コメントを適切に追加した（特に複雑な部分）
- 関連するドキュメントを更新した
- 変更によって新しい警告は発生していない
- テストを追加/更新した
- すべてのテストが通過する

### 8. PR作成コマンドを実行

```bash
# PRを作成（タイトルと本文を指定）
gh pr create --title "feat: add shell linting environment" --body "$(cat <<'EOF'
## Summary

シェルスクリプトの品質を保証するため、shellcheck と shfmt を使用したリンティング環境を構築します。これにより、バグの早期発見とコードスタイルの統一が可能になります。

## Type of Change

- [x] New feature (non-breaking change which adds functionality)

## Related Issues

Closes #20

## Changes

- GitHub Actions ワークフロー (.github/workflows/shell-linting.yml) を追加
  - PR と main ブランチへのプッシュで自動実行
  - shellcheck による静的解析
  - shfmt によるフォーマットチェック

- Docker 環境を追加
  - Dockerfile.shell-linting: shellcheck と shfmt をインストール
  - docker-compose.shell-linting.yml: 簡単に実行可能な設定

## Test Plan

- [x] shellcheck が全スクリプトでエラーなく実行される
- [x] shfmt のフォーマットチェックが通る
- [ ] GitHub Actions ワークフローが正常に動作する
- [ ] Docker 環境でリンティングが実行できる

## Checklist

- [x] コードはプロジェクトのスタイルガイドに従っています
- [x] 自己レビューを実施しました
- [x] 関連するドキュメントを更新しました
- [x] テストを追加/更新しました

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

## PR品質チェックポイント

| 項目 | チェック内容 |
|------|--------------|
| **タイトル** | Conventional Commits形式（type: description） |
| **Summary** | 目的と背景が2-3文で明確 |
| **Type of Change** | 適切な変更種別を選択 |
| **Related Issues** | Issue番号が正確（Closes/Fixes/Related to） |
| **Changes** | 変更内容が階層的で詳細 |
| **Test Plan** | テスト方法が具体的でチェックリスト形式 |
| **Checklist** | 全項目を確認済み |
| **Breaking Change** | 破壊的変更がある場合は明記 |

## 重要な注意事項

1. **コミット履歴の分析**: PRを作成する前に必ずコミット履歴を確認
2. **What/Why/Howの明確化**: レビュアーが理解しやすい構成
3. **テンプレート準拠**: 既存のPRテンプレート形式に従う
4. **Issue連携**: Related Issuesで正確にIssueを参照
5. **Claude Code署名**: PR本文の末尾に署名を追加
6. **チェックリスト確認**: 提出前に全項目をチェック

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yellow-seed) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

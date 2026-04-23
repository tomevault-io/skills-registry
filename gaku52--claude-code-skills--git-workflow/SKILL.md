---
name: git-workflow
description: Git運用・ブランチ戦略・コミット規約・PR管理の包括的なガイド。ブランチ戦略の選択、コミットメッセージ規約、コンフリクト解決、Git hooks活用など、Gitに関する全ての判断基準と手順を提供します。 Use when this capability is needed.
metadata:
  author: gaku52
---

# Git Workflow Skill

## 📋 目次

1. [概要](#概要)
2. [いつ使うか](#いつ使うか)
3. [ブランチ戦略](#ブランチ戦略)
4. [コミット規約](#コミット規約)
5. [PR管理](#pr管理)
6. [よくある問題](#よくある問題)
7. [Agent連携](#agent連携)

---

## 概要

このSkillは、Gitを使った開発ワークフローの全てをカバーします：

- ✅ ブランチ戦略の選択と運用
- ✅ コミットメッセージ規約（Conventional Commits）
- ✅ PR作成・レビュー・マージプロセス
- ✅ コンフリクト解決手法
- ✅ Git hooks活用
- ✅ リベース vs マージの判断基準
- ✅ 過去の失敗パターンと対策

## 📚 公式ドキュメント・参考リソース

**このガイドで学べること**: ブランチ戦略の選択、コミット規約、PR運用、コンフリクト解決、Git hooks活用
**公式で確認すべきこと**: 最新のGit機能、GitHub/GitLab新機能、セキュリティベストプラクティス

### 主要な公式ドキュメント

- **[Git Documentation](https://git-scm.com/doc)** - Git公式ドキュメント
  - [Git Book](https://git-scm.com/book/en/v2)
  - [Reference Manual](https://git-scm.com/docs)

- **[GitHub Documentation](https://docs.github.com/)** - GitHub公式ガイド
  - [Pull Requests](https://docs.github.com/en/pull-requests)
  - [Actions](https://docs.github.com/en/actions)

- **[Conventional Commits](https://www.conventionalcommits.org/)** - コミットメッセージ規約
  - [Specification](https://www.conventionalcommits.org/en/v1.0.0/)

- **[GitLab Documentation](https://docs.gitlab.com/)** - GitLab公式ドキュメント
  - [Git Workflows](https://about.gitlab.com/topics/version-control/what-is-gitlab-flow/)

### 関連リソース

- **[Atlassian Git Tutorials](https://www.atlassian.com/git/tutorials)** - Git学習リソース
- **[Oh Shit, Git!?!](https://ohshitgit.com/)** - Git問題解決ガイド
- **[Git Flight Rules](https://github.com/k88hudson/git-flight-rules)** - Git緊急対応ガイド

---

## いつ使うか

### 自動的に参照されるケース

- 新しいブランチを作成する時
- コミットを作成する時
- PRを作成する時
- マージコンフリクトが発生した時
- Git操作で問題が発生した時

### 手動で参照すべきケース

- プロジェクト開始時のブランチ戦略決定
- Git運用ルールの見直し時
- チームメンバーへのGit教育時

---

## ブランチ戦略

### 戦略の選択

プロジェクトに応じて最適なブランチ戦略を選択：

| 戦略 | 適用ケース | 詳細ガイド |
|------|-----------|-----------|
| **GitHub Flow** | 継続的デプロイ、小規模チーム | [guides/01-github-flow.md](guides/01-github-flow.md) |
| **Git Flow** | 定期リリース、大規模プロジェクト | [guides/02-git-flow.md](guides/02-git-flow.md) |
| **Trunk-Based** | 高頻度デプロイ、成熟したCI/CD | [guides/03-trunk-based.md](guides/03-trunk-based.md) |

### ブランチ命名規則

```
<type>/<ticket-number>-<short-description>

例:
feature/PROJ-123-add-user-authentication
bugfix/PROJ-456-fix-memory-leak
hotfix/PROJ-789-critical-crash-fix
```

詳細: [guides/04-branch-naming.md](guides/04-branch-naming.md)

---

## コミット規約

### Conventional Commits

全てのコミットメッセージは以下の形式に従う：

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Type一覧

| Type | 説明 | 例 |
|------|------|-----|
| `feat` | 新機能 | `feat(auth): add biometric authentication` |
| `fix` | バグ修正 | `fix(ui): resolve layout issue on iPad` |
| `refactor` | リファクタリング | `refactor(network): simplify API client` |
| `perf` | パフォーマンス改善 | `perf(images): implement lazy loading` |
| `test` | テスト追加・修正 | `test(login): add unit tests for validation` |
| `docs` | ドキュメント | `docs(readme): update installation steps` |
| `chore` | ビルド・設定等 | `chore(deps): update dependencies` |
| `ci` | CI/CD | `ci(github): add caching to workflow` |

詳細ガイド: [guides/05-commit-messages.md](guides/05-commit-messages.md)

### コミット前チェックリスト

→ [checklists/pre-commit.md](checklists/pre-commit.md)

---

## PR管理

### PR作成時

1. **タイトル**: コミットメッセージ形式に従う
2. **説明**: テンプレートを使用 → [templates/pull-request-template.md](templates/pull-request-template.md)
3. **レビュワー**: 適切な担当者をアサイン
4. **ラベル**: 種類・優先度・ステータス

### PR説明テンプレート

```markdown
## 概要
<!-- 何を変更したか簡潔に -->

## 変更内容
<!-- 主な変更点を箇条書き -->
-
-

## 動作確認
<!-- テスト方法・確認項目 -->
- [ ] 単体テスト実行
- [ ] 実機での動作確認
- [ ] UIテスト実行

## スクリーンショット
<!-- 必要に応じて -->

## 関連Issue
Closes #XXX
```

### PRレビューガイド

→ [guides/06-pr-review.md](guides/06-pr-review.md)

### PRマージ前チェックリスト

→ [checklists/pre-merge.md](checklists/pre-merge.md)

---

## よくある問題

### コンフリクト解決

詳細: [guides/07-conflict-resolution.md](guides/07-conflict-resolution.md)

### リベース vs マージ

| 状況 | 推奨 | 理由 |
|------|------|------|
| featureブランチの更新 | Rebase | 履歴を綺麗に保つ |
| mainへのマージ | Merge (Squash) | PRの履歴を保持 |
| 公開済みブランチ | Merge | 履歴改変を避ける |

詳細: [guides/08-rebase-vs-merge.md](guides/08-rebase-vs-merge.md)

### 過去のトラブル事例

→ [incidents/index.md](incidents/index.md)

---

## Agent連携

### このSkillを使用するAgents

1. **branch-creator-agent**
   - イシューから自動的にブランチ作成
   - 命名規則を自動適用
   - Thoroughness: `quick`

2. **commit-validator-agent**
   - コミットメッセージ検証
   - Conventional Commits準拠チェック
   - Thoroughness: `quick`

3. **pr-reviewer-agent**
   - PR自動レビュー
   - チェックリスト確認
   - Thoroughness: `medium`

4. **conflict-resolver-agent**
   - コンフリクト分析
   - 解決方法提案
   - Thoroughness: `thorough`

### 推奨Agentワークフロー

#### PR作成時（並行実行）

```
pr-validator-agent (quick) +
commit-history-checker-agent (quick) +
branch-policy-checker-agent (quick)
→ 結果統合 → PRコメント
```

#### マージ前（順次実行）

```
final-review-agent (thorough)
→ test-runner-agent (medium)
→ merge-safety-checker-agent (quick)
→ マージ実行
```

### Agentパラメータ例

```markdown
Task: PRの自動レビューを実行

Agent: pr-reviewer-agent
Skill: git-workflow
Thoroughness: medium
Parameters:
  - check_commit_messages: true
  - check_branch_name: true
  - check_pr_description: true
  - auto_comment: true
```

---

## クイックリファレンス

### よく使うコマンド

```bash
# 新しいfeatureブランチ作成
git checkout -b feature/PROJ-123-description

# コミット
git add .
git commit -m "feat(scope): description"

# mainの変更を取り込む（rebase）
git fetch origin
git rebase origin/main

# PR用にpush
git push -u origin feature/PROJ-123-description
```

### トラブルシューティング

| 問題 | 解決方法 | 詳細 |
|------|---------|------|
| コンフリクト発生 | [guides/07-conflict-resolution.md](guides/07-conflict-resolution.md) | 段階的解決手順 |
| 間違ったコミット | [references/undo-commands.md](references/undo-commands.md) | reset, revert使い分け |
| ブランチ間違い | [references/branch-recovery.md](references/branch-recovery.md) | cherry-pickで復旧 |

---

## 詳細ドキュメント

### Guides（詳細ガイド）

1. [GitHub Flow完全ガイド](guides/01-github-flow.md)
2. [Git Flow完全ガイド](guides/02-git-flow.md)
3. [Trunk-Based Development](guides/03-trunk-based.md)
4. [ブランチ命名規則](guides/04-branch-naming.md)
5. [コミットメッセージ規約](guides/05-commit-messages.md)
6. [PRレビューガイド](guides/06-pr-review.md)
7. [コンフリクト解決](guides/07-conflict-resolution.md)
8. [Rebase vs Merge](guides/08-rebase-vs-merge.md)
9. [Git Hooks活用](guides/09-git-hooks.md)
10. [トラブルシューティング](guides/10-troubleshooting.md)

### Checklists（チェックリスト）

- [コミット前チェックリスト](checklists/pre-commit.md)
- [PR作成前チェックリスト](checklists/pre-pr.md)
- [マージ前チェックリスト](checklists/pre-merge.md)
- [リリースブランチチェックリスト](checklists/release.md)

### Templates（テンプレート）

- [PRテンプレート](templates/pull-request-template.md)
- [コミットメッセージテンプレート](templates/commit-message-template.md)
- [Issue テンプレート](templates/issue-template.md)

### References（リファレンス）

- [ベストプラクティス集](references/best-practices.md)
- [アンチパターン集](references/anti-patterns.md)
- [よくある落とし穴](references/common-pitfalls.md)
- [Gitコマンドリファレンス](references/git-commands.md)
- [元に戻すコマンド集](references/undo-commands.md)
- [ブランチ復旧方法](references/branch-recovery.md)

### Incidents（過去の問題事例）

- [インシデント一覧](incidents/index.md)
- [2024年の主要インシデント](incidents/2024/)

---

## 学習リソース

- 📚 [Pro Git Book](https://git-scm.com/book/ja/v2)
- 📖 [Conventional Commits](https://www.conventionalcommits.org/)

---

## 更新履歴

このSkill自体の変更履歴は [CHANGELOG.md](CHANGELOG.md) を参照

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gaku52) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

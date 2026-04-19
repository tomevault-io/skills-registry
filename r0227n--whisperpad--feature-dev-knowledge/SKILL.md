---
name: feature-dev-knowledge
description: Git worktree feature development workflow for WhisperPad. Use for: single-feature implementation with TCA layer separation, automatic commit splitting, merge to current branch, test worktree for validation. Keywords: worktree, feature, git gtr, TCA, Models, Clients, Features, commit strategy, merge, test, parallel development. Use when this capability is needed.
metadata:
  author: r0227n
---

# Feature Development Workflow Skill

このスキルは、git gtr (git-worktree-runner) を使用した feature 開発ワークフローのナレッジベースです。

## 概要

WhisperPad プロジェクトにおける機能開発の標準ワークフロー:

1. 独立した worktree で機能を実装
2. TCA レイヤー別にコミットを分割
3. 元のブランチにマージ
4. テスト worktree で検証
5. テスト完了後に最終マージ

## 前提条件

- WhisperPad プロジェクト（TCA アーキテクチャ）
- git gtr (git-worktree-runner) インストール済み

## 基本ワークフロー

1. **要件分析** - 機能要件から必要なファイルを特定
2. **Worktree 作成** - `git gtr new feature/<name>`
3. **TCA レイヤー別実装** - Models → Clients → Features → App
4. **コミット分割** - レイヤーごとに論理的コミット
5. **マージ** - 実行元ブランチにマージ（`--no-ff`）
6. **テスト worktree 作成** - `git gtr new test/<current-branch>`
7. **テスト実行と修正ループ** - 合格するまで繰り返し（最大 5 回）
8. **テストブランチマージ** - 実行元ブランチにマージ
9. **Worktree 削除** - `git gtr rm feature/<name>` と `git gtr rm test/<branch>`

## TCA Layer Separation Strategy

### Layer 1: Models

- **独立性**: 高
- **変更リスク**: 低
- **実装順序**: 1st
- **並列実装**: 可能
- **例**: `AppSettings.swift`, `WhisperModel.swift`

### Layer 2: Clients

- **独立性**: 中
- **変更リスク**: 中
- **実装順序**: 2nd-3rd
- **並列実装**: Models 完了後に可能（Interface と Live は並列可）
- **例**:
  - Interface: `AudioRecorderClient.swift`
  - Live: `AudioRecorderClientLive.swift`

### Layer 3: Features

- **独立性**: 低
- **変更リスク**: 中
- **実装順序**: 4th
- **並列実装**: 不可（Models/Clients 依存）
- **例**: `RecordingFeature.swift`, `SettingsFeature.swift`

### Layer 4: App

- **独立性**: 最低
- **変更リスク**: 高
- **実装順序**: 5th（最後）
- **並列実装**: 不可（すべてに依存）
- **例**: `AppReducer.swift`, `AppDelegate.swift`

## 並列開発戦略

### 並列実装可能なレイヤー

```
Models (独立)
  ├─ 並列実装可能
  └─ 同時に複数のModelを実装

Clients (Models依存)
  ├─ Models完了後に開始
  ├─ Interface と Live は並列実装可能
  └─ 複数のClientを並列実装可能

Features (Models/Clients依存)
  ├─ Models/Clients完了後に開始
  └─ 独立したFeatureは並列実装可能

App (すべて依存)
  ├─ すべて完了後に開始
  └─ 統合作業のため並列不可
```

### 並列開発の実装方法

```bash
# git gtr ai で複数worktree内のClaude Codeを同時起動
# Terminal 1: Models layer実装
git gtr ai feature/new-feature

# Terminal 2: Clients layer実装（Models完了後）
git gtr ai feature/new-feature

# 各セッションでTodoWriteを使用してタスク管理
# 依存関係を明示的に記述
```

## Conflict Avoidance Rules

### 同じファイルの編集

- **ルール**: 同じファイルを編集する場合は、同じ worktree で作業
- **理由**: マージコンフリクトの回避
- **例**: `SettingsFeature.swift` を複数機能で変更する場合

### 共有 State/Action

- **ルール**: 共有 State/Action の変更は最終統合フェーズで実施
- **理由**: 複数機能で同時変更するとコンフリクト発生
- **例**: `AppReducer.State` への新フィールド追加

### 特定ファイルの編集順序

- **SettingsFeature.swift**: 最後に編集（多くの機能が依存）
- **AppDelegate.swift**: 最後に編集（アプリケーション全体に影響）
- **Models/\*.swift**: 最初に編集（他レイヤーが依存）

## Commit Message Strategy

### フォーマット

```
<type>(<scope>): <description>

<body>

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>
```

### Type の種類

- `feat`: 新機能追加
- `fix`: バグ修正
- `refactor`: リファクタリング（機能変更なし）
- `test`: テスト追加・修正
- `docs`: ドキュメント更新
- `style`: コードスタイル修正（フォーマット等）
- `perf`: パフォーマンス改善
- `chore`: ビルドプロセス・補助ツール変更

### Scope の種類

TCA レイヤーに対応:

- `models`: Models layer
- `clients`: Clients layer 全般
- `clients-interface`: Client interface
- `clients-live`: Client live implementation
- `features`: Features layer 全般
- `features-recording`: Recording feature
- `features-settings`: Settings feature
- `app`: App layer
- `misc`: その他

### 例

```
feat(models): Add NotificationSettings model

新しい通知設定用のモデルを追加

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>
```

## Test Worktree Strategy

### テスト worktree の目的

1. **独立した環境でテスト** - 実装 worktree と分離
2. **テスト失敗の修正** - 本番ブランチを汚さない
3. **リグレッション検証** - 既存機能への影響確認

### テストループ

```bash
MAX_ATTEMPTS=5
attempt=0

while [ $attempt -lt $MAX_ATTEMPTS ]; do
    # テスト実行
    mise run test

    if [ $? -eq 0 ]; then
        # 成功
        break
    else
        # 失敗時の処理
        attempt=$((attempt + 1))
        # 失敗分析と修正
        # コミット
    fi
done
```

### テスト失敗時の対応

1. **ログ分析**: エラーメッセージを確認
2. **Task ツールで修正**: 専門 agent に依頼
3. **コミット**: 修正内容を記録
4. **再実行**: 再度テスト実行
5. **最大 5 回まで**: 無限ループ回避

## Best Practices

### 1. 小さく始める

- **MVP (Minimum Viable Product)** から開始
- 大きな機能は複数の小さな機能に分割
- 各機能ごとに独立した worktree で開発

### 2. レイヤー順守

- **下層から上層へ**: Models → Clients → Features → App
- 依存関係を明確に
- 逆方向の依存を避ける

### 3. 早期コミット

- **レイヤー完成ごとにコミット**
- コミットサイズを適切に保つ
- コミットメッセージを明確に

### 4. テスト駆動

- **各レイヤーでテスト**
- テスト worktree で検証
- リグレッションテストを実施

### 5. レビュー

- **マージ前に diff レビュー**
- `git diff develop..feature/xxx` で変更確認
- 不要な変更がないか確認

### 6. 並列開発の活用

- **独立したレイヤーは並列実装**
- TodoWrite で依存関係を管理
- コンフリクトを事前に予防

## Examples

### Example 1: 新しい Client 追加

新しい通知機能を追加する例：

```bash
# 1. worktree作成
git gtr new feature/notification-client

# 2. git gtr ai で実装開始
git gtr ai feature/notification-client

# 3. 実装順序（レイヤー別）
# Layer 1: Models
touch WhisperPad/Models/NotificationSettings.swift
# NotificationSettings struct を実装

# Layer 2: Clients (Interface)
touch WhisperPad/Clients/NotificationClient.swift
# NotificationClient protocol を実装

# Layer 2: Clients (Live)
touch WhisperPad/Clients/NotificationClientLive.swift
# NotificationClient.live を実装

# Layer 3: Features
# SettingsFeature.swift に action/state を追加
# GeneralSettingsTab.swift に UI を追加

# Layer 4: App
# AppReducer.swift に dependency を登録

# 4. コミット分割
git add WhisperPad/Models/NotificationSettings.swift
git commit -m "feat(models): Add NotificationSettings model"

git add WhisperPad/Clients/NotificationClient.swift
git commit -m "feat(clients-interface): Add NotificationClient protocol"

git add WhisperPad/Clients/NotificationClientLive.swift
git commit -m "feat(clients-live): Implement NotificationClient.live"

git add WhisperPad/Features/Settings/*
git commit -m "feat(features-settings): Add notification settings UI"

git add WhisperPad/App/AppReducer.swift
git commit -m "feat(app): Register NotificationClient dependency"

# 5. マージとテスト（メインセッションで実行）
git merge feature/notification-client --no-ff
git gtr new test/current-branch
git gtr ai test/current-branch
# テスト実行・修正
git merge test/current-branch --no-ff
```

### Example 2: 既存 Feature 拡張

Recording 機能に無音検出を追加する例：

```bash
# 1. worktree作成
git gtr new feature/silence-detection

# 2. git gtr ai で実装開始
git gtr ai feature/silence-detection

# 3. 実装順序（既存Feature拡張のため、Modelsは不要）
# Layer 2: Clients
# AudioRecorderClient.swift に silenceThreshold を追加
# AudioRecorderClientLive.swift に無音検出ロジック実装

# Layer 3: Features
# RecordingFeature.swift に state 拡張
# RecordingView.swift に UI 更新

# Layer 4: App
# AppReducer.swift に統合更新（必要な場合）

# 4. コミット分割
git add WhisperPad/Clients/AudioRecorderClient*.swift
git commit -m "feat(clients): Add silence detection to AudioRecorderClient"

git add WhisperPad/Features/Recording/*
git commit -m "feat(features-recording): Add silence detection UI and logic"

# 5. マージとテスト
git merge feature/silence-detection --no-ff
git gtr new test/current-branch
# ... テスト実行
```

### Example 3: 並列開発

複数の独立した機能を同時に開発する例：

```bash
# Terminal 1: Notification機能実装
git gtr new feature/notification
git gtr ai feature/notification
# Models → Clients → Features → App の順で実装

# Terminal 2: Audio Filter機能実装（並列）
git gtr new feature/audio-filter
git gtr ai feature/audio-filter
# Models → Clients → Features → App の順で実装

# 両方の実装が完了後、順番にマージ
# Terminal 1
git merge feature/notification --no-ff

# Terminal 1でマージ完了後、Terminal 2
git merge feature/audio-filter --no-ff

# テストworktreeで統合テスト
git gtr new test/integration
git gtr ai test/integration
# 両機能の統合テスト実行
```

## Troubleshooting

### worktree 作成失敗

**症状**: `git gtr new` がエラーで失敗

**原因**:

- 既存 worktree と名前が重複
- ディスク容量不足

**対処**:

```bash
# 既存worktree確認
git gtr list

# 重複している場合は削除
git gtr rm <existing-branch>

# ディスク容量確認
df -h
```

### マージコンフリクト

**症状**: マージ時にコンフリクト発生

**原因**:

- 同じファイルを複数 worktree で編集
- ベースブランチが更新された

**対処**:

```bash
# コンフリクト確認
git status

# 手動でコンフリクト解決
# ファイルを編集して <<<<<<<, =======, >>>>>>> を削除

# 解決後
git add .
git commit
```

### テスト無限ループ

**症状**: テストが繰り返し失敗

**原因**:

- テストコードのバグ
- 環境依存の問題

**対処**:

- 最大試行回数制限（5 回）により自動停止
- AskUserQuestion でユーザーに確認
- 手動でテストログをレビュー

### git gtr ai が起動しない

**症状**: `git gtr ai` コマンドが失敗

**原因**:

- git gtr がインストールされていない
- 設定が不正

**対処**:

```bash
# git gtr インストール確認
which git-gtr

# インストールされていない場合
git clone https://github.com/coderabbitai/git-worktree-runner.git
cd git-worktree-runner
sudo ln -s "$(pwd)/bin/git-gtr" /usr/local/bin/git-gtr
```

## Advanced Topics

### Custom Commit Strategies

プロジェクト固有のコミット戦略をカスタマイズ可能:

```bash
# 例: UI変更とロジック変更を分離
git add *View.swift
git commit -m "feat(features): Update UI for new feature"

git add *Feature.swift
git commit -m "feat(features): Add business logic for new feature"
```

### Multi-Worktree Development

複数の worktree を同時に使用した開発:

```bash
# Feature開発用
git gtr new feature/main-feature

# Bugfix用（並列）
git gtr new fix/critical-bug

# Experiment用（並列）
git gtr new experiment/new-approach

# すべて完了後、順番にマージ
git merge feature/main-feature --no-ff
git merge fix/critical-bug --no-ff
git merge experiment/new-approach --no-ff
```

### Integration with CI/CD

テスト worktree を CI/CD パイプラインと統合:

```bash
# テストworktreeでCI実行
git gtr new test/ci-integration
git gtr ai test/ci-integration

# mise run test でローカルテスト
mise run test

# GitHub Actions等のCIでもテスト
git push origin test/ci-integration
# CI がトリガーされる
```

## Related Skills

- `parallel-dev:parallel-dev`: 複数 feature 並列開発
- `component-dev`: コンポーネント単位開発
- `pr-create`: Pull Request 作成
- `commit-strategy`: コミット分割戦略

## References

- [git-worktree-runner Documentation](https://github.com/coderabbitai/git-worktree-runner)
- [TCA Documentation](https://github.com/pointfreeco/swift-composable-architecture)
- WhisperPad `CLAUDE.md` - プロジェクト固有ガイドライン
- WhisperPad `docs/spec.md` - 詳細仕様

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/r0227n) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

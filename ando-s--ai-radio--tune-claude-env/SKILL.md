---
name: tune-claude-env
description: 開発体験を改善するためにClaude Code環境を最適化する。コードベースの実態を分析し、コンテキスト効率を最大化するrules/agents/skills/hooksを生成・更新する。 Use when this capability is needed.
metadata:
  author: ando-s
---

# Claude Code 環境チューニング

コードベースの実態を分析し、**コンテキストを効率よく使う**ための設定を生成・更新する。

対象: $ARGUMENTS（省略時は all）

## 原則: なぜコンテキスト効率が重要か

Claude Code のコンテキストウィンドウは有限。全情報を常時ロードするとすぐ埋まる。
→ **必要な情報を、必要な時だけ、必要な粒度で**ロードする設計にする。

| 仕組み | 役割 | コンテキストコスト |
|---|---|---|
| CLAUDE.md | 常時ロード。最小限に | 高（常に消費） |
| rules/ | パス条件で自動ロード | 低（関連ファイル編集時のみ） |
| agents/ | サブエージェントの独立コンテキスト | ゼロ（メイン会話から分離） |
| skills/ | /コマンド時のみロード | ゼロ（呼び出し時のみ） |
| hooks | 自動実行、コンテキスト外 | ゼロ（バックグラウンド） |
| memory/ | 常時ロード。200行以内 | 中（常に消費） |

## Step 1: コードベース分析

### 1a. ファイル構造の把握
```
Glob: src/**/*.{ts,tsx}
```
ディレクトリ構成、ファイル数、命名パターンを確認。

### 1b. コードパターンの検出
以下を検索して、プロジェクト固有のパターンを特定:
- `export function` — コンポーネント/関数の命名規約
- `type Props` / `type.*=` — 型定義パターン
- `useCallback` / `useReducer` / `useRef` — hooks パターン
- `export async function POST` — API ルートパターン
- `import.*from` — 依存関係パターン

### 1c. 頻出ワークフローの検出
git log から最近のコミットパターンを分析:
```
git log --oneline -20
```
どんな種類の変更が多いか（機能追加、バグ修正、リファクタ等）。

## Step 2: 各設定の分析と改善

### 2a. rules/ の改善

**分析**: 各 rule の `paths` が実ファイルにマッチするか Glob で確認。
ルールの内容が実コードパターン（Step 1b で検出）を反映しているか確認。

**改善方針**:
- パスが古い / マッチしない → 更新
- 新しいディレクトリ・パターンにルールがない → 作成
- ルールが薄い（実パターンが書かれていない）→ 具体的なパターンを追記
- ルールが大きすぎる → 分割（paths を狭く）

**ルール作成のテンプレート**:
```markdown
---
paths:
  - "具体的なglobパターン"
---
# ルール名
- 規約1
- 規約2
```

### 2b. agents/ の改善

**分析**: 各エージェントのプロジェクト構造記述が最新か確認。

**改善方針**:
- プロジェクト構造が古い → 最新のファイル一覧に更新
- チェック項目が実パターンを反映していない → 更新
- 新しいユースケース（テスト実行、パフォーマンス分析等）→ 新エージェント作成
- model 選択: 調査系は haiku（安い・速い）、判断系は sonnet

**エージェント作成のテンプレート**:
```markdown
---
name: エージェント名
description: 1行説明
tools: "使用ツール"
model: "haiku or sonnet"
maxTurns: 8-15
---
具体的な指示
```

### 2c. skills/ の改善

**分析**: 繰り返し行うワークフローが skill 化されているか確認。

**改善方針**:
- 繰り返すワークフロー（デプロイ、リリース、テスト等）→ skill 作成
- 手動でやっている定型作業 → skill 化を検討
- disable-model-invocation: true にすべきか判断（副作用あり→true、知識→false）
- `!`command`` で動的コンテキスト注入を活用

**skill 作成のテンプレート**:
```markdown
---
name: スキル名
description: いつ使うか、何をするか
disable-model-invocation: true  # ユーザー明示呼び出しのみ
---
手順
```

### 2d. hooks の改善

**分析**: 自動化できるチェック・処理がないか確認。

**hooks の候補**:
| イベント | 用途 | 例 |
|---|---|---|
| PostToolUse (Write\|Edit) | 保存後の自動チェック | lint, type-check, format |
| Stop | タスク完了確認 | ビルド通るか確認 (prompt型) |
| UserPromptSubmit | コンテキスト注入 | 最近の git 変更を注入 |
| SessionStart | 環境セットアップ | 依存インストール確認 |
| PreToolUse (Bash) | 危険コマンド防止 | rm -rf 等をブロック |

**hooks は settings.local.json に追記**。スクリプトは `.claude/hooks/` に配置。

### 2e. CLAUDE.md の改善

**分析**:
- 行数を確認（目標: 80行以下）
- Compact Instructions セクションがあるか
- コーディングに不要な情報（背景、コンセプト等）がないか
- rules/ に移せる情報がないか

**改善方針**:
- 背景情報・コンセプト → 削除（README.md 等に移動）
- パス固有の規約 → rules/ に移動
- アーキテクチャの要点だけ残す
- Compact Instructions を先頭に配置

### 2f. memory/ の改善

**分析**:
- 200行以内か
- アーキテクチャ情報が最新か
- 不要な一時情報がないか

**改善方針**:
- セッション固有の情報 → 削除
- 実装済み機能リスト → 最新化
- セマンティックに整理（時系列ではなくトピック別）

## Step 3: 実行

分析結果に基づいて、実際にファイルを作成・更新する。
変更の前に、何をどう変えるか説明してユーザーの確認を取る。

## Step 4: 検証

`npm run build` でビルドが通ることを確認（設定変更がコードに影響しないことの確認）。
変更した設定ファイルの一覧と変更理由を報告。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ando-s) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

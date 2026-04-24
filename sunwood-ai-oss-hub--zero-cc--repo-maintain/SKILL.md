---
name: repo-maintain
description: | Use when this capability is needed.
metadata:
  author: sunwood-ai-oss-hub
---

# GitHub Repository Maintainer

既存のGitHubリポジトリのメンテナンス作業を支援します。

## 前提条件

- GitHub CLI (`gh`) がインストール済み
- `gh auth login` で認証済み
- Gitリポジトリ内であること

## ワークフロー

### 引数解析
`$ARGUMENTS` から操作タイプを特定:

| 操作 | 引数パターン | 説明 |
|:----|:-------------|:------|
| **release** | `release [ver]`, `rl [ver]`, `publish [ver]` | リリース作成 |
| **changelog** | `changelog`, `changes`, `history` | 変更履歴生成 |
| **issue** | `issue [title]` | イシュー作成 |
| **status** | `status`, `st` | 状態確認 |

---

## release - リリース作成

Git タグと GitHub リリースを作成します。

### 手順

1. **現在の状態確認**
   ```bash
   git fetch --tags
   git tag -l | tail -5
   git log --oneline -10
   git status --short
   ```

   **重要**: 保留中の変更がある場合は、先にコミットするか確認する

2. **バージョン決定**
   - 引数指定 → 使用
   - 未指定 → 現在のタグから自動推奨（例: v1.0.0 → v1.0.1）

3. **変更内容収集**
   ```bash
   PREV_TAG=$(git describe --tags --abbrev=0 2>/dev/null || echo "")
   git log ${PREV_TAG}..HEAD --pretty=format:"%h %s" --reverse
   git diff ${PREV_TAG}...HEAD --stat
   ```

4. **リリースノート生成**

   まず `references/RELEASE_NOTES.md` のフォーマットを参照:
   ```
   .claude/skills/repo-maintain/references/RELEASE_NOTES.md
   ```

   コミットメッセージを解析して分類:

   | プレフィックス/Emoji | カテゴリ |
   |:---------------------|:----------|
   | `feat:`, `✨` | 新機能 |
   | `fix:`, `🐛` | バグ修正 |
   | `refactor:`, `🔄` | リファクタリング |
   | `perf:` | パフォーマンス |
   | `docs:`, `📝` | ドキュメント |
   | `test:` | テスト |
   | `chore:` | その他 |
   | `style:` | スタイル |
   | なし | 変更 |

   **バイリンガルリリースノートの構成:**
   ```markdown
   <img src="https://raw.githubusercontent.com/[user]/[repo]/main/assets/release-header-v[X.Y.Z].svg" alt="v[X.Y.Z] Release"/>

   # v[X.Y.Z] - [タイトル] / [English Title]

   **リリース日 / Release Date:** YYYY-MM-DD

   ---

   ## 日本語 / Japanese

   ### 概要
   [リリースの概要]

   ### 新機能
   - 機能1
   - 機能2

   ### バグ修正
   - 修正1
   - 修正2

   ### 変更
   - 変更1
   - 変更2

   ---

   ## English

   ### Overview
   [Release overview]

   ### What's New
   - Feature 1
   - Feature 2

   ### Bug Fixes
   - Fix 1
   - Fix 2

   ### Changes
   - Change 1
   - Change 2
   ```

5. **リリースヘッダー画像のカスタマイズ**

   `references/release-header.svg` をベースに、バージョン固有のテーマを適用:

   **カスタマイズ項目:**
   - **カラーテーマ**: バージョンのテーマに合わせた色（例: v0.2.0 = 青系/フロー）
   - **グラデーション**: テキスト、背景、バージョン番号のグラデーション
   - **エフェクト**: テーマに応じたエフェクト（波形、粒子など）
   - **アニメーション**: 色、動きの調整

   **手順:**
   1. `assets/release-header-v[X.Y.Z].svg` を作成
   2. テンプレートのプレースホルダーを埋める
   3. 色とエフェクトをカスタマイズ
   4. プロジェクトルートの `RELEASE_NOTES.md` から参照

6. **リリース実行（gh コマンド）**

   生成した `RELEASE_NOTES.md` を使用して GitHub リリースを作成:

   ```bash
   # タグ作成
   git tag -a v[version] -m "v[version] - [Release Title]"

   # タグプッシュ
   git push origin v[version]

   # リリース作成（マークダウンファイルを指定）
   gh release create v[version] \
     --title "v[version] - [Release Title] (YYYY-MM-DD)" \
     --notes-file RELEASE_NOTES.md
   ```

   **ポイント:**
   - `--notes-file` で生成済みのマークダウンを直接指定
   - ヘッダー画像は assets/ に配置済みであること
   - リリース後、README が更新必要か確認

7. **完了後の処理**
   - リリースURLを表示
   - README の更新が必要か確認
   - 次の開発サイクルへの準備

---

## changelog - 変更履歴生成

直近の変更履歴を生成・表示します。

```bash
PREV_TAG=$(git describe --tags --abbrev=0 2>/dev/null || echo "")
git log ${PREV_TAG}..HEAD --pretty=format:"%h %s" --reverse
git diff ${PREV_TAG}...HEAD --stat
```

カテゴリ別に分類して表示（Emoji コンベンション対応）

---

## issue - イシュー作成

GitHub イシューを作成します。

```bash
gh issue create --title "[title]" --body "[description]" --label "bug,enhancement"
```

**イシューテンプレート:**
```markdown
## 概要
[問題の概要]

## 再現手順
1. 手順1
2. 手順2

## 期待する動作
[期待]

## 実際の動作
[現状]

## 環境
- OS:
- Version:
```

---

## status - リポジトリ状態確認

リポジトリの状態をサマリー表示します。

```bash
echo "=== Git Status ==="
git status --short
echo ""
echo "=== Branch ==="
git branch --show-current
echo ""
echo "=== Recent Commits ==="
git log --oneline -5
echo ""
echo "=== GitHub Info ==="
gh repo view --json name,url,visibility,latestRelease 2>/dev/null
echo ""
echo "=== Open PRs ==="
gh pr list --state open --limit 5 2>/dev/null
echo ""
echo "=== Open Issues ==="
gh issue list --state open --limit 5 2>/dev/null
```

---

## 使用例

```bash
/repo-maintain release 1.0.0
/repo-maintain changelog
/repo-maintain issue "Bug: Login fails"
/repo-maintain status
```

---

## 関連スキル

| スキル | 用途 |
|:------|:------|
| **repo-flow** | フィーチャーブランチ作成、PR作成、マージ |
| **repo-create** | 新規リポジトリ作成 |
| **extension-generator** | Claude Code 拡張機能の自動生成 |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sunwood-ai-oss-hub) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

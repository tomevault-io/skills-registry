---
name: oasis-article
description: Generate blog articles for Qiita and Zenn from GitHub release information. Use when creating release notes or technical articles that follow specific markdown formats with frontmatter. Supports generating articles from release data, creating custom formatted articles, and saving articles to specified repositories for both Qiita and Zenn platforms Use when this capability is needed.
metadata:
  author: sunwood-ai-oss-hub
---

# Oasis Article Generator

指定したGitHubリポジトリのリリース情報を元に、Qiita/Zenn用の記事を生成するスキル。

## Quick Start

基本の使用方法:

```bash
# Qiita記事を生成
/oasis-article <org>/<repo> <version> --platform qiita

# Zenn記事を生成
/oasis-article <org>/<repo> <version> --platform zenn

# 両方のプラットフォームに生成
/oasis-article <org>/<repo> <version> --platform all

# 例
/oasis-article Sunwood-ai-labs/oasis-sync v0.2.4 --platform qiita
```

## Workflow

### Step 1: リリース情報の取得

GitHub CLI (`gh`) を使用してリリース情報を取得:

```bash
gh release view <version> --repo <org>/<repo> --json title,body,tagName,createdAt,publishedAt,author
```

### Step 2: 画像URLの抽出

リリースノートの本文から最初の画像URLを抽出:

```bash
# リリースノートのbodyから画像URLを抽出
# 正規表現パターン: !\[.*?\]\((https://[^)]+)\)
# 最初に見つかった画像URLを使用
```

画像URLが見つからない場合は、デフォルトのプレースホルダーを使用:
- Qiita: `![image](https://raw.githubusercontent.com/<org>/<repo>/main/generated-images/<path>)`
- Zenn: `![image](https://github.com/user-attachments/assets/<placeholder>)`

### Step 3: リポジトリ情報の取得

必要に応じて追加情報を取得:

```bash
# コミット履歴を取得
git log <previous-version>..<version> --oneline

# 変更されたファイルを確認
git diff <previous-version>..<version> --stat
```

### Step 4: 記事の生成

以下の要素を含めて記事を生成:

1. **Frontmatter**: 記事のメタデータ（プラットフォーム別）
2. **紹介文**: リリースの概要
3. **変更点**: 主要な変更のリスト
4. **技術的な詳細**: 新機能、改善点、バグ修正
5. **参考リンク**: 関連URL

### Step 5: 記事の保存

生成した記事を指定したリポジトリに保存:

```bash
# Qiita
qiita-article/public/<YYYYMMDD>-<slug>-<version>.md
# 例: 20260129-claude-glm-actions-lab-v1-1-0.md

# Zenn
zenn/articles/<YYYY-MM-DD>-<slug>.md
# 例: 2026-01-29-claude-glm-actions-lab-v1-1-0.md
```

**重要**: ファイル名にバージョン番号を含める場合、ドット（.）をハイフン（-）に変換してください。
- ❌ `v1.1.0` → NG（ドットが含まれる）
- ✅ `v1-1-0` → OK（ドットをハイフンに変換）

## Platform Formats

### Qiita Format

**重要**: 新規記事の場合は `id: ""` （空文字列）を指定してください。

```yaml
---
title: 【リリースノート】<repo> <version> - <description>
tags:
- Tag1
- Tag2
- Tag3
- Tag4
- Tag5
private: false
updated_at: null
id: ""
organization_url_name: null
slide: false
ignorePublish: false
---
```

**制約事項**:
- **タグは最大5つまで**
- **`id` は必須フィールド**: 新規記事は `id: ""`、既存記事の更新はQiitaの記事IDを指定

### Zenn Format

```yaml
---
title: <emoji> <repo> <version> リリース！<description>
emoji: "<emoji>"
type: tech
topics:
- oasis
- github-actions
- gemini
- zenn
- qiita
published: true
---
```

**制約事項**:
- **slugは12〜50文字**: 半角英数字（a-z0-9）、ハイフン（-）、アンダースコア（_）のみ
- **ドット（.）は使用不可**: バージョン番号の `v1.1.0` は `v1-1-0` に変換する

## Article Structure

```markdown
![画像URL](https://raw.githubusercontent.com/<org>/<repo>/main/generated-images/<path>)

## はじめに
本日、`<repo>` のバージョン `<version>` をリリースしました。このアップデートは、<主な改善点>です。

## 主な変更点
今回のリリースにおける主な変更点は以下の通りです。

- **変更点1**: 説明
- **変更点2**: 説明
- **変更点3**: 説明

## 技術的な詳細
### 新機能
#### 機能名
説明...

### メンテナンス
説明...

## まとめ
`<repo> <version>` は、<主な改善点>のための重要な一歩です。

| 項目 | 改善内容 | メリット |
|:---|:---|:---|
| **項目1** | 内容 | メリット |
| **項目2** | 内容 | メリット |

---
### 📚 参考リンク
- **GitHubリポジトリ**: [<org>/<repo>](https://github.com/<org>/<repo>)
- **リリースページ**: [<version> Release](https://github.com/<org>/<repo>/releases/tag/<version>)
- **変更点の比較**: [<prev>...<version> の差分](https://github.com/<org>/<repo>/compare/<prev>...<version>)
```

## Resources

### assets/

テンプレートファイルを含む:

- `qiita-template.md` - Qiita記事のテンプレート
- `zenn-template.md` - Zenn記事のテンプレート

## エラー対策 (Troubleshooting)

### Zennのエラー

#### ファイル名が不正です
```
articles/2026-01-29-repo-v1.1.0.mdはファイル名が不正です
```

**原因**: ファイル名にドット（.）が含まれている

**解決策**: ドットをハイフンに変換
- ❌ `2026-01-29-repo-v1.1.0.md`
- ✅ `2026-01-29-repo-v1-1-0.md`

### Qiitaのエラー

#### タグは1つ以上、5つ以内で指定してください
```
タグは1つ以上、5つ以内で指定してください
```

**原因**: frontmatterのタグが5つを超えている

**解決策**: タグを5つ以内に減らす
```yaml
tags:
- Tag1  # 重要なタグ5つを選択
- Tag2
- Tag3
- Tag4
- Tag5
# Tag6  # 削除
```

#### idは文字列で入力してください
```
idは文字列で入力してください
```

**原因**: `id` フィールドが存在しない、または型が正しくない

**解決策**: `id: ""` （空文字列）を追加
```yaml
id: ""  # 新規記事の場合は空文字列
```

#### 記事が見つかりませんでした（404）
```
QiitaNotFoundError: Not found
記事が見つかりませんでした
```

**原因**: 存在しない記事IDで更新しようとしている

**解決策**:
- 新規記事: `id: ""` を指定
- 既存記事更新: Qiitaの記事IDを確認して指定

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sunwood-ai-oss-hub) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

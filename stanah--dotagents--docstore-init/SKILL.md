---
name: docstore-init
description: > Use when this capability is needed.
metadata:
  author: stanah
---

# docstore-init: ドキュメント構造からフォーマット定義を生成

既存リポジトリのドキュメント構造を分析し、`.docstore/metadata-format.md` を自動生成するスキル。
生成されたフォーマット定義は `doc-to-repo` の Step 5 でオーバーライドとして読み込まれ、プロジェクト固有の慣習に合ったメタデータフォーマットで抽出が行われる。

## ワークフロー

### Step 1: 既存ドキュメント構造のスキャン

プロジェクトルートからドキュメントファイルを検索・収集する。

1. `Glob` ツールで以下のパターンを検索する:
   - `README.md`, `README*.md`
   - `docs/**/*.md`
   - `**/*.md`（ただし `node_modules/`, `.git/`, `.docstore/`, `.claude/` は除外）
2. 見つかったファイルのディレクトリ構造とファイル一覧を整理する。
3. ドキュメントが全く見つからない場合は、デフォルトのフォーマット定義をそのまま `.docstore/metadata-format.md` にコピーして終了する。

### Step 2: パターン分析

見つかったドキュメントファイル（最大20ファイル程度）を `Read` ツールで読み取り、以下のパターンを分析する:

#### 2-1. 言語
- ファイル内容が主に日本語か英語かを判定する。
- 混在している場合は主要言語を特定する。

#### 2-2. 見出しスタイル
- `#` の使い方（ATX スタイル vs Setext スタイル）。
- 見出しの命名規則（日本語 vs 英語、名詞句 vs 動詞句）。

#### 2-3. Frontmatter
- YAML frontmatter の有無。
- 使用されているフィールド（title, date, tags, category 等）。

#### 2-4. ファイル命名規則
- kebab-case, snake_case, camelCase, PascalCase 等のパターンを検出する。

#### 2-5. ディレクトリ構造
- `docs/` のサブディレクトリ構成（カテゴリ分け方法）。

#### 2-6. 要約スタイル
- 箇条書き中心か文章中心かを判定する。

### Step 3: metadata-format.md 生成

1. `doc-to-repo` スキルのデフォルトフォーマット定義を読み込む:
   - パス: `.claude/skills/doc-to-repo/references/metadata-format.md`
2. デフォルト定義をベースに、Step 2 の分析結果を反映したカスタム版を生成する。
3. カスタマイズのポイント:
   - `content.language` のデフォルト値をプロジェクトの主要言語に設定
   - `content.summary` の記述スタイル指示をプロジェクトの慣習に合わせる（箇条書き vs 文章）
   - frontmatter フィールドが検出された場合、`content` に対応フィールドを追加
   - ファイル命名規則に関する注記を追加
   - ディレクトリ構造の慣習に関する注記を追加
   - プロジェクト固有の追加フィールドやルールがあれば追加
4. 生成したファイルを `.docstore/metadata-format.md` に出力する。
   - ファイル先頭に「このファイルは docstore-init により自動生成された」旨のコメントを含める。
   - 生成日時を記載する。

### Step 4: 結果報告

検出したパターンと生成内容のサマリーを以下の形式で表示する:

```
## docstore-init 完了

### 検出したパターン
- **言語**: <ja | en | mixed>
- **見出しスタイル**: <ATX (#) | Setext (===)>
- **Frontmatter**: <あり（fields: ...） | なし>
- **ファイル命名規則**: <kebab-case | snake_case | ...>
- **ディレクトリ構造**: <概要>
- **要約スタイル**: <箇条書き中心 | 文章中心>
- **分析ファイル数**: <n>

### 生成ファイル
- `.docstore/metadata-format.md`

### カスタマイズ内容
- <適用した変更点のリスト>
```

## 注意事項

- `.docstore/metadata-format.md` が既に存在する場合は、上書きするかユーザーに確認する（AskUserQuestion を使用）。
- デフォルトフォーマットの構造（meta.yaml テンプレート、sources.yaml テンプレート、raw.md ルール）は必ず維持する。プロジェクト固有の変更は追加・調整のみ。
- 分析対象が多すぎる場合は、代表的なファイル（README.md、docs/ 直下のファイル等）を優先して読み取る。
- `references/` ディレクトリは不要。`doc-to-repo` のものを参照する。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stanah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

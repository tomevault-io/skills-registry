---
name: doc-to-repo
description: > Use when this capability is needed.
metadata:
  author: stanah
---

# doc-to-repo: 外部ファイル抽出スキル

外部ファイル（PDF, PPTX, DOCX, 画像, テキスト等）からテキストとメタデータを抽出し、`.docstore/` に構造化された中間ファイルとして保存する。

## ワークフロー

### Step 1: 入力検証

1. 引数からファイルパスを取得する。引数がない場合はユーザーに確認する。
2. ファイルが存在するか確認する（`ls` で確認）。
3. ファイル形式を拡張子から判定する（対応形式: pdf, pptx, ppt, docx, png, jpg, jpeg, gif, txt, md）。
4. `.docstore/sources.yaml` が存在する場合、同じファイルが既に抽出済みかチェックする。
   - 既に抽出済みの場合、ユーザーに上書きするか確認する（AskUserQuestion を使用）。

### Step 2: ID生成

- ファイル名（拡張子除く）からkebab-case IDを生成する。
- 変換ルール:
  - スペース、アンダースコア → ハイフン
  - 大文字 → 小文字
  - 連続ハイフン → 単一ハイフン
  - 先頭・末尾のハイフン除去
- 例: `The-Complete-Guide-to-Building-Skill-for-Claude.pdf` → `the-complete-guide-to-building-skill-for-claude`

### Step 3: ディレクトリ作成

```bash
mkdir -p .docstore/extracted/<id>/sections
```

### Step 4: コンテンツ抽出

形式に応じた方法で読み取りを行い、`raw.md` に保存する。

#### PDF の場合
- Read ツールを使用してPDFを読み取る（Readツールは PDF を直接読める）。
- ページ数が多い場合は `pages` パラメータで分割して読み取る（1回あたり最大20ページ）。
- 読み取った内容を `.docstore/extracted/<id>/raw.md` に保存する。

#### PPTX の場合
- `python3 -c "from pptx import Presentation; ..."` で読み取りを試みる。
- python-pptx がない場合はユーザーに `pip install python-pptx` を案内する。
- 各スライドのテキストを抽出して `raw.md` に保存する。

#### DOCX の場合
- `python3 -c "from docx import Document; ..."` で読み取りを試みる。
- python-docx がない場合はユーザーに `pip install python-docx` を案内する。
- 段落テキストを抽出して `raw.md` に保存する。

#### 画像 (PNG/JPG/GIF) の場合
- Read ツールを使用して画像を読み取る（Claude は画像を視覚的に理解できる）。
- 画像から読み取れるテキストや内容の説明を `raw.md` に保存する。

#### テキスト/Markdown の場合
- Read ツールでそのまま読み取り、`raw.md` にコピーする。

### Step 5: フォーマット定義の読み込み

メタデータのフォーマット定義を以下の優先順位で読み込む:

1. `.docstore/metadata-format.md` （プロジェクトカスタム版、あれば優先）
2. このスキルの `references/metadata-format.md` （デフォルト）

読み込んだフォーマット定義に従って、以降の Step 6, 7 を実行する。

### Step 6: 構造化メタデータ生成

抽出した `raw.md` の内容を分析し、Step 5 で読み込んだフォーマット定義に従って `meta.yaml` を生成する。

### Step 7: sources.yaml 更新

Step 5 で読み込んだフォーマット定義に従って、`.docstore/sources.yaml` を読み込み（なければ新規作成）、エントリを追加/更新する。

### Step 8: 結果報告

抽出結果のサマリーを以下の形式で表示する:

```
## 抽出完了

- **ファイル**: <filename>
- **ID**: <id>
- **タイトル**: <title>
- **形式**: <type>
- **ステータス**: complete
- **セクション数**: <n>
- **主要トピック**: <topics>

保存先: .docstore/extracted/<id>/
```

## 注意事項

- `raw.md` には生テキストをできるだけそのまま保持する。要約や加工は `meta.yaml` 側で行う。
- 大きなファイルの場合、`sections/` ディレクトリにセクション分割して保存してもよい。
- エラーが発生した場合、`meta.yaml` の `extraction.status` を `partial` または `failed` に設定する。
- このスキルはフェーズ1（抽出）のみを担当する。フェーズ2（統合・配置）は `doc-integrate` スキルが担当する。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stanah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

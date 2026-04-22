---
name: docstore-search
description: > Use when this capability is needed.
metadata:
  author: stanah
---

# docstore-search: ドキュメント検索・参照スキル

`.docstore/` 内のドキュメントをメタデータと全文の両方で横断検索する read-only スキル。

## ワークフロー

### Step 1: クエリ解析

1. 引数から検索クエリを取得する。引数がない場合はエラー終了（「検索クエリを指定してください」と表示）。
2. クエリの種類を判定する:
   - **トピック検索**: 単一キーワードまたは kebab-case トピック（例: `skill`, `api-design`）
   - **キーワード検索**: 複数キーワードまたはフレーズ（例: `authentication best practices`）
   - **質問回答**: 疑問文（例: `スキルの作り方は？`）

### Step 2: メタデータ検索

1. `.docstore/extracted/` 配下の全 `meta.yaml` を Glob で検出し、Read で読み込む。
2. 各 `meta.yaml` の以下のフィールドに対してクエリとのマッチングを行う:
   - `content.title`: タイトル一致
   - `content.topics`: トピック一致（完全一致 + 部分一致）
   - `content.summary`: 要約内のキーワード一致
   - `content.key_takeaways`: 重要ポイント内のキーワード一致
   - `content.sections[].title`: セクションタイトル一致
3. マッチしたフィールドと一致度に基づいてスコアを算出する:
   - topics 完全一致: +10
   - title 一致: +8
   - key_takeaways 一致: +5
   - summary 一致: +3
   - sections.title 一致: +2

### Step 3: 全文検索（メタデータでヒット不足の場合）

メタデータ検索でスコア付きの結果が3件未満の場合:

1. Grep ツールで `.docstore/extracted/*/raw.md` を検索する。
2. マッチした行のコンテキスト（前後3行）を取得する。
3. 全文マッチした結果をスコア +1 で結果リストに追加する。

### Step 4: 結果表示

スコア降順で結果を表示する:

```
## 検索結果: "<query>"

**ヒット数**: <n>件

### 1. <title> (スコア: <score>)
- **ID**: <id>
- **ソース**: <file>
- **トピック**: <topics>
- **マッチ箇所**: <matched_fields>
- **要約**: <summary>
- **統合先**: <target_path or "未統合">

### 2. <title> (スコア: <score>)
...
```

質問回答モードの場合は、最もスコアの高いドキュメントの関連部分を引用して回答を提示する。

### Step 5: クロスリファレンス

1. 検索結果のドキュメント間で共通するトピックを検出する。
2. 共通トピックがある場合、関連ドキュメントのリンクを表示する:
   ```
   ### 関連ドキュメント
   - <doc_a> と <doc_b> は共通トピック "<topic>" を持っています。
   ```

## 注意事項

- このスキルはファイルを一切変更しない（read-only）。
- `meta.yaml` が存在しないドキュメントは全文検索のみ対象とする。
- 検索結果が0件の場合は「該当するドキュメントが見つかりませんでした」と表示する。
- 大量のドキュメント（50件以上）がある場合は、上位10件のみ表示し、`--all` 相当の指示があれば全件表示する。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stanah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

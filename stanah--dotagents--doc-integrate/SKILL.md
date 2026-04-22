---
name: doc-integrate
description: > Use when this capability is needed.
metadata:
  author: stanah
---

# doc-integrate: ドキュメント統合・配置スキル

`.docstore/extracted/` に抽出済みのドキュメントを、プロジェクトの `docs/` ディレクトリに統合・配置する。`doc-to-repo` のフェーズ2に相当する。

## ワークフロー

### Step 1: 対象特定

1. 引数でドキュメントID が指定されている場合、そのドキュメントを対象とする。
2. 引数がない場合、`.docstore/sources.yaml` を読み込み、`integrated: false` のエントリ一覧を表示する。
3. 複数ある場合は AskUserQuestion で対象を選択させる。一括統合も選択肢に含める。
4. 対象ドキュメントの `.docstore/extracted/<id>/meta.yaml` と `raw.md` を読み込む。

### Step 2: 配置先決定

1. `.docstore/metadata-format.md` が存在すれば読み込み、プロジェクト慣習を把握する。
2. `meta.yaml` の `content.topics` と `content.title` を参照する。
3. 既存の `docs/` ディレクトリ構造を Glob でスキャンし、適切な配置先を推定する。
4. 配置先パスの候補を生成する（例: `docs/guides/<id>.md`, `docs/<topic>/<id>.md`）。
5. AskUserQuestion でユーザーに配置先を確認する。カスタムパスの入力も許可する。

### Step 3: ドキュメント変換

1. `raw.md` の内容をベースに、プロジェクト慣習に合った Markdown に変換する:
   - frontmatter の追加（プロジェクトで使用されている場合）
   - 見出しレベルの調整（プロジェクトの慣習に合わせる）
   - 言語の統一（プロジェクトの主要言語に合わせる）
2. `meta.yaml` の `content.summary` と `content.key_takeaways` を活用して、冒頭に概要セクションを追加する。
3. `meta.yaml` の `content.sections` 構造を参考に、見出し構造を整理する。

### Step 4: ファイル配置

1. 配置先ディレクトリが存在しない場合は作成する。
2. Write ツールで変換済み Markdown を配置先パスに書き込む。
3. 書き込み後、ファイルが正しく作成されたか確認する。

### Step 5: sources.yaml 更新

`.docstore/sources.yaml` の対象エントリを更新する:
- `integrated: true`
- `target_path: "<配置先の相対パス>"`
- `integrated_date: "<YYYY-MM-DD>"`
- `last_updated` を現在日付に更新

### Step 6: インデックス更新（オプション）

1. `docs/README.md` または `docs/index.md` が存在するか確認する。
2. 存在する場合、新しいドキュメントへのリンクを適切なセクションに追加する。
3. 存在しない場合はスキップする（強制的にインデックスを作成はしない）。

### Step 7: 結果報告

統合結果のサマリーを以下の形式で表示する:

```
## 統合完了

- **ドキュメント**: <title>
- **ID**: <id>
- **配置先**: <target_path>
- **変換内容**: <frontmatter追加, 見出し調整 等>
- **統合日**: <YYYY-MM-DD>

次のステップ:
- `/docstore-status` で全体の状態を確認
- `/doc-code-sync` でコードとの整合性をチェック
```

## 注意事項

- `raw.md` の内容を尊重し、過度な加工は行わない。構造整理と慣習適合が目的。
- 配置先が既に存在する場合は、上書きするかユーザーに確認する。
- 一括統合の場合は、各ドキュメントごとに配置先を確認する（自動推定 + 一括承認も可）。
- `docs/` ディレクトリが存在しない場合は作成する。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stanah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

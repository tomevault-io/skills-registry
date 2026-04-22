---
name: doc-update
description: > Use when this capability is needed.
metadata:
  author: stanah
---

# doc-update: ドキュメント差分更新スキル

ソースファイルが更新された場合に、再抽出と再統合を行う差分更新パイプライン。

## ワークフロー

### Step 1: 変更検出

1. `.docstore/sources.yaml` を読み込む。存在しない場合はエラー終了。
2. 各エントリについて、ソースファイルの mtime と `extracted` 日付を比較する。
   - Bash で `stat -f %m <file>` (macOS) または `stat -c %Y <file>` (Linux) を使用。
   - mtime を YYYY-MM-DD に変換して比較する。
3. ソースファイルが抽出日より新しいエントリを「要更新」リストに追加する。
4. 引数でドキュメント ID が指定されている場合は、そのドキュメントのみを対象とする（mtime に関わらず強制更新）。
5. 要更新リストが空の場合は「すべてのドキュメントは最新です」と表示して終了する。

### Step 2: 更新対象の確認

1. 要更新リストをテーブル形式で表示する:
   ```
   | ID | ファイル | 抽出日 | ソース更新日 |
   ```
2. AskUserQuestion で更新方法を確認する:
   - 一括更新（すべて）
   - 個別選択
   - キャンセル

### Step 3: 再抽出

選択された各ドキュメントに対して:

1. 既存の `.docstore/extracted/<id>/raw.md` をバックアップとして `raw.md.bak` にリネームする。
2. `doc-to-repo` の Step 4（コンテンツ抽出）と同じ方法でソースファイルを再読み取りする。
3. 新しい `raw.md` を生成する。
4. `meta.yaml` を再生成する（`doc-to-repo` の Step 5-6 相当）。
5. `sources.yaml` の `extracted` 日付と `source_modified` を更新する。
6. 差分の要約を記録する（新旧の `meta.yaml` の topics, sections 数の変化等）。

### Step 4: 統合先の更新

`integrated: true` のドキュメントについて:

1. `target_path` のファイルが存在するか確認する。
2. 存在する場合、`doc-integrate` の Step 3（ドキュメント変換）と同じ方法で再変換する。
3. 変換後のコンテンツで `target_path` のファイルを更新する。
4. `sources.yaml` の `integrated_date` を更新する。
5. `target_path` が存在しない場合は、AskUserQuestion で再配置するか確認する。

### Step 5: 差分レポート表示

更新結果を以下の形式で表示する:

```
## 更新完了

### 更新されたドキュメント
| ID | ファイル | 変更点 |
|----|---------|--------|
| <id> | <file> | セクション数: 5→7, トピック追加: <new_topic> |

### 統合先の更新
| ID | 統合先 | 状態 |
|----|--------|------|
| <id> | <target_path> | 更新済み |

### バックアップ
- 旧版は `.docstore/extracted/<id>/raw.md.bak` に保存されています。
```

## 注意事項

- 再抽出前に必ず旧データのバックアップを取る（`raw.md.bak`）。
- 差分の検出は日付レベルで行う。同日内の複数回更新は検出しない。
- 統合先の更新は、元の配置時と同じ変換ルールを適用する。
- `extraction.status` が `failed` のエントリも再抽出の対象に含める。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stanah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

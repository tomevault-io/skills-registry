---
name: docstore-status
description: > Use when this capability is needed.
metadata:
  author: stanah
---

# docstore-status: ドキュメントストア状態確認スキル

`.docstore/` 内のドキュメント状態をダッシュボード形式で表示する read-only スキル。ファイル変更は一切行わない。

## ワークフロー

### Step 1: sources.yaml 読み込み

1. `.docstore/sources.yaml` を読み込む。
2. 存在しない場合は「docstore が初期化されていません。`/docstore-init` を実行してください。」と表示して終了する。

### Step 2: 各ドキュメントの状態チェック

`sources.yaml` の各エントリに対して以下をチェックする:

1. **ソースファイル存在チェック**: 元ファイル（`file` フィールド）が存在するか確認する。
2. **抽出データ存在チェック**: `.docstore/extracted/<id>/raw.md` と `meta.yaml` が存在するか確認する。
3. **陳腐化チェック**: ソースファイルの mtime と `extracted` 日付を比較する。ソースが新しければ「要更新」と判定する。
   - Bash で `stat -f %m <file>` (macOS) または `stat -c %Y <file>` (Linux) を使用。
4. **統合先チェック**: `integrated: true` の場合、`target_path` のファイルが実際に存在するか確認する。

各ドキュメントの状態を以下のいずれかに分類する:
- `OK`: 抽出済み・統合済み・最新
- `NOT_INTEGRATED`: 抽出済みだが未統合
- `STALE`: ソースファイルが抽出後に更新されている
- `BROKEN_TARGET`: 統合先ファイルが存在しない
- `MISSING_SOURCE`: ソースファイルが見つからない
- `EXTRACTION_FAILED`: 抽出ステータスが failed

### Step 3: 統計集計

以下の統計を集計する:
- 総ドキュメント数
- 各ステータスの件数
- 統合済み / 未統合 の件数
- 最終更新日

### Step 4: ダッシュボード表示

以下の形式でターミナルに表示する:

```
## Docstore Status

**最終更新**: <last_updated>
**総ドキュメント数**: <n>

| ID | タイトル | ソース | 状態 | 抽出日 | 統合先 |
|----|---------|--------|------|--------|--------|
| <id> | <title> | <file> | <status> | <date> | <target_path or "-"> |

### サマリー
- OK: <n>
- 未統合: <n>
- 要更新: <n>
- 問題あり: <n>

### 推奨アクション
- <状態に応じた推奨アクションのリスト>
  例: "2件の未統合ドキュメントがあります。`/doc-integrate` で統合してください。"
  例: "1件のドキュメントが陳腐化しています。`/doc-update` で更新してください。"
```

## 注意事項

- このスキルはファイルを一切変更しない（read-only）。
- `meta.yaml` が存在する場合はタイトルをそこから取得する。存在しない場合は ID を表示する。
- mtime の比較は日付レベル（YYYY-MM-DD）で行う。秒単位の精度は不要。
- ドキュメント数が多い場合（20件以上）は、問題のあるもののみデフォルト表示し、`--all` 相当の指示があれば全件表示する。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stanah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

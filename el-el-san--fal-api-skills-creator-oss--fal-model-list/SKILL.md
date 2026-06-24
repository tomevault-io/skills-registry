---
name: fal-model-list
description: fal.ai の全モデル一覧を取得・検索・フィルタする。キーワード検索、カテゴリ絞り込み、複数の出力形式に対応。 Use when this capability is needed.
metadata:
  author: el-el-san
---

# fal.ai Model List

fal.ai Platform API から全モデル一覧を取得するスキル。

## 使い方

```bash
# 全モデル一覧（カテゴリ別）
python .claude/skills/fal-model-list/scripts/fal_model_list.py

# キーワード検索
python .claude/skills/fal-model-list/scripts/fal_model_list.py -q "kling"

# カテゴリで絞り込み
python .claude/skills/fal-model-list/scripts/fal_model_list.py -c "text-to-image"

# 検索 + カテゴリ絞り込み
python .claude/skills/fal-model-list/scripts/fal_model_list.py -q "flux" -c "image-to-image"

# カテゴリ一覧だけ表示
python .claude/skills/fal-model-list/scripts/fal_model_list.py --categories

# ファイルに保存
python .claude/skills/fal-model-list/scripts/fal_model_list.py -o models.txt
```

## 出力フォーマット (`-f`)

| フォーマット | 説明 |
|---|---|
| `grouped` | カテゴリ別にグループ化（デフォルト） |
| `table` | `endpoint_id [category] display_name` のテーブル |
| `ids` | endpoint_id のみ（1行1ID） |
| `json` | endpoint_id の JSON 配列 |
| `detail` | メタデータ付き JSON |

```bash
# endpoint_id だけ欲しい場合
python .claude/skills/fal-model-list/scripts/fal_model_list.py -f ids

# 詳細 JSON
python .claude/skills/fal-model-list/scripts/fal_model_list.py -q "veo" -f detail
```

## API 仕様

- エンドポイント: `https://api.fal.ai/v1/models`
- 認証不要（公開 API）
- ページネーション: `limit` (max 500) + `cursor`
- 検索: `q` パラメータ

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/el-el-san) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

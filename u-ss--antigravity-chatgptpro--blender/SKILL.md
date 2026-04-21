---
name: blender-agent-v1-2-0
description: Blender 5.0汎用生成エージェント（Universal Orchestrator + Specialist Packs） Use when this capability is needed.
metadata:
  author: u-ss
---

# Blender Agent SKILL v1.2.0## コンセプト

## 役割境界

- この SKILL.md は技術仕様（入出力・判定基準・実装詳細）の正本。
- 実行手順は同フォルダの WORKFLOW.md を参照。


`/blender` は **汎用オーケストレーター**。

- 何を作るかは `asset_spec` に正規化
- どう作るかは `domain` ごとの専門パックで切替
- 品質は `validation` で数値判定し、未達は自動修正ループ
- 完成物は `self_review` で再判定して最終ステータスを確定

## 実行モード

### 1. Universalモード（既定）
- `tools/blender_bridge/universal_agent.py`
- domain自動推定（house/product/furniture/vehicle/scene/prop/character）
- 反復: `build_universal_asset.py -> validate_universal_asset.py`

### 2. Domain Specialist（house）
- `domain=house` かつ `--prefer-house-agent` 時は `house_agent.py` を優先利用
- 失敗時は Universalへフォールバック

### 3. Domain Specialist（character）
- `domain=character` かつ `--prefer-character-agent` 時は `character_agent.py` を優先利用
- 失敗時は Universalへフォールバック

### 4. Live Editing（任意）
- `--open-gui --interactive`
- 生成後の `final.blend` を開いたまま対話編集

## エージェント部隊（運用設計）

| 役割 | 実装モジュール | 出力 |
|:-----|:---------------|:-----|
| Intent/Spec正規化 | `universal_spec.py` | `asset_spec_normalized.json` |
| Asset Scout + License Guard | `asset_pipeline.py` | `asset_report` |
| Build | `tools/blender_bridge/scripts/build_universal_asset.py` | `iter_*.blend`, `iter_*_*.png` |
| Validate/Repair | `tools/blender_bridge/scripts/validate_universal_asset.py` + `apply_repair_actions` | `validation_iter_*.json` |
| Finalize | `universal_agent.py` | `final.*`, `run_report.json` |

Specialist packs:

- House: `house_agent.py` + `house_spec.py` + `tools/blender_bridge/scripts/build_house_v4.py` + `tools/blender_bridge/scripts/validate_house.py`
- Character: `character_agent.py` + `character_spec.py` + `character_blueprint.py` + `tools/blender_bridge/scripts/build_character_v1.py` + `tools/blender_bridge/scripts/validate_character.py`

## 主要契約

- `tools/blender_bridge/contracts/asset_spec.schema.json`
- `tools/blender_bridge/contracts/asset_validation.schema.json`
- `tools/blender_bridge/contracts/asset_manifest.schema.json`

### asset_manifest 最小例

```json
{
  "assets": [
    {
      "id": "chair_wood_01",
      "path": "C:/assets/chair_wood_01.glb",
      "license": "CC0",
      "domains": ["furniture", "scene"],
      "tags": ["chair", "wood"],
      "quality_score": 0.86
    }
  ]
}
```

## 実運用ガードレール

1. ライセンス
- allow/deny を先に評価
- 非許可ライセンスは自動拒否

2. 品質
- 寸法・トポロジ・ポリゴン予算を数値検証
- `pass=false` 時は `repair_actions` で最大3回修正

3. 可観測性
- `run_report.json` に仮定・選定理由・失敗理由を保存
- 失敗時も成果物とログ位置を残す

4. 互換性
- `ag_rpc_client.py` の公開メソッド名は変更しない

## 🧩 子エージェント一覧

| 子エージェント | パス | 役割 |
|:---------------|:-----|:-----|
| Character | `sub_agents/character/SPEC.md` | キャラモデリング専用（部位指向生成・設計図生成・品質検証） |
| House | `sub_agents/house/SPEC.md` | 実在戸建て高精度モデリング（8ステージパイプライン） |
| Image World | `sub_agents/image_world/SPEC.md` | 参照画像ベースで空間を生成 |

`domain=house` の場合は `sub_agents/house/SPEC.md` を読んで実行。
`domain=character` の場合は `sub_agents/character/SPEC.md` を読んで実行。

## 代表コマンド

```powershell
# Promptのみpython tools/blender_bridge/universal_agent.py --prompt "近未来の小型ドローン"

# 生成前に仕様とアセット選定だけ確認python tools/blender_bridge/universal_agent.py --prompt "木製チェア" --asset-manifest .\asset_manifest.json --dry-run

# アセット利用 + ライセンス制御python tools/blender_bridge/universal_agent.py `
  --prompt "北欧風リビング" --domain scene `
  --asset-manifest .\asset_manifest.json `
  --allow-licenses "CC0,CC-BY,ROYALTYFREE,INTERNAL"

# GUI編集python tools/blender_bridge/universal_agent.py --prompt "展示台付きプロダクト" --open-gui --interactive
```

## Rules

- ライセンス不明アセットはデフォルト拒否
- 反復修正の上限を超えたら `NEEDS_INPUT` で終了
- run_reportなしで完了扱いにしない
- Language: 日本語

##  ログ記録（WorkflowLogger統合）

> [!IMPORTANT]
> 実行時は必ずWorkflowLoggerで各フェーズをログ記録すること。
> 詳細: [WORKFLOW_LOGGING.md](../shared/WORKFLOW_LOGGING.md)

`python
import sys; sys.path.insert(0, '.agent/workflows/shared')
from workflow_logging_hook import logged_main, phase_scope
`

ログ保存先: `_logs/autonomy/{agent}/{YYYYMMDD}/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/u-ss) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

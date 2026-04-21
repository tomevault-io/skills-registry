---
name: unity-mcp-agent-v1-0-0
description: Unity Editor を MCP 経由で操作し、ジャンル別ゲーム制作の計画・実装キュー・検証ループを生成するエージェント Use when this capability is needed.
metadata:
  author: u-ss
---

# Unity MCP Agent SKILL v1.0.0
**技術詳細**: Unity+MCPで複数ジャンルのゲームを安定して作るための入力仕様、生成仕様、品質評点仕様。

## 役割境界

- この SKILL.md は技術仕様（入出力、生成ルール、評点ロジック）の正本。
- 実行手順は同フォルダの WORKFLOW.md を参照。
- Unity Editor への直接操作は MCP ツール経由のみ（手動UI操作前提にはしない）。

## 入力仕様

- `title` (必須): ゲームタイトル
- `genre` (必須): `action_3d | platformer_2d | puzzle_3d | tower_defense | racing_arcade`
- `platforms` (任意): 既定 `["Windows"]`
- `constraints` (任意): 制約文字列配列
- `core_loop` (任意): 既定はジャンルテンプレートから補完
- `min_score` (任意): 既定 `96`

## 出力仕様

- `game_spec.json`: 仕様定義
- `game_roadmap.md`: 実装ロードマップ
- `mcp_task_queue.json`: Unity MCP 実行キュー
- `dispatch_queue.json`: 実行接続キュー（`/code` `/check`）
- `quality_report.json`: 評点結果（100点満点）
- `run_manifest.json`: 実行メタ情報

## 生成ルール

1. ジャンルテンプレートから「必須システム」「推奨MCPツール」を読み込む
2. タスクを `setup_project → scene_blockout → gameplay → polish → verify` の順で生成
3. `verify` には最低限 `recompile_scripts`, `get_console_logs`, `run_tests` を含める
4. 実行キューは依存関係 (`depends_on`) を持つトポロジ順で出力する
5. 低品質時は改善ループで不足タスクを補い再評点する

## 評点ルーブリック（100点）

- Spec Completeness: 30点
- Phase Coverage: 25点
- Safety Controls: 20点
- Genre System Coverage: 15点
- Artifact Completeness: 10点

合格条件:
- `total_score >= min_score`
- かつ `total_score > 95`

## Rules

- Unity 操作コマンドは MCP ツール名を明示する
- 破壊的変更前にバックアップ手順をキューへ含める
- 95点未満なら改善ループを継続する
- 報告言語は日本語

## ログ記録（WorkflowLogger統合）

> [!IMPORTANT]
> 実行時は WorkflowLogger でフェーズログを残すこと。
> 詳細: `../shared/WORKFLOW_LOGGING.md`

ログ保存先: `_logs/autonomy/{agent}/{YYYYMMDD}/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/u-ss) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

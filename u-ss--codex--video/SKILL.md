---
name: video-agent-v1-1-0
description: 動画制作統合エージェント — DAGオーケストレーション + 子エージェント群 Use when this capability is needed.
metadata:
  author: u-ss
---

# Video Agent SKILL v1.1.0

**動画制作パイプライン全体を統括する統合エージェント。**

## 役割境界

- この SKILL.md は動画制作全体の技術仕様と子エージェント管理の正本。
- 実行手順は同フォルダの WORKFLOW.md を参照。
- 各専門処理は `sub_agents/` 配下の子エージェントに委譲する。

## 📖 概要

`A0` としてステップ依存を管理し、`run_state.json` と成果物参照を一元管理する。
各ステップは専門の子エージェントが担当し、親がDAG順序で実行を制御する。

## 🧩 子エージェント一覧

| 子エージェント | パス | 役割 | パイプラインStep |
|:---------------|:-----|:-----|:----------------|
| Director | `sub_agents/director/SPEC.md` | Soraプロンプト補強・品質レポート | D1 |
| ShotList | `sub_agents/shotlist/SPEC.md` | shot_list入力契約検証 | A1 |
| Asset | `sub_agents/asset/SPEC.md` | 素材収集・命名・重複排除 | A2 |
| Probe | `sub_agents/probe/SPEC.md` | ffprobe測定・Conform生成 | A3 |
| Timing | `sub_agents/timing/SPEC.md` | タイムライン構築・字幕最適化 | A5 |
| VoiceVox | `sub_agents/voicevox/SPEC.md` | VOICEVOX音声生成 | A4 |
| RemotionProps | `sub_agents/remotion_props/SPEC.md` | Remotion props変換 | A6 |
| Renderer | `sub_agents/renderer/SPEC.md` | Remotionレンダリング実行 | A7 |
| Audio | `sub_agents/audio/SPEC.md` | ナレーション+BGMミックス | A8 |
| Finalize | `sub_agents/finalize/SPEC.md` | 最終mux・ラウドネス正規化 | A9 |

## 入力

- `projects/<project_slug>/shot_list.json`
- 既存成果物（`--resume` 時）

## 出力

- `run_state.json`（ステップ状態: `pending/running/success/failed/skipped`）
- ステップ成果物のパス参照

## 実装要件

- `D1 Director` を先行実行して `shot_list` を安全補強
- `sora_quality_report.json` に `severity=error` があれば停止
- `A2` と `A4` の並列実行
- 失敗時に `error_type` `message` `recovery_hint` を記録
- `--from` `--to` `--resume` `--force` をサポート
- 有料APIは呼ばない（Soraはブラウザ手動運用）

## Rules

- 破壊的操作は行わない
- 復帰可能な状態管理を優先する
- `A2` 失敗時は素材追加後に `--from a2_collect --resume` で再実行
- Language: 日本語

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/u-ss) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

---
name: vc-core
description: vc の解析/メタ生成/CLI 基本操作 Use when this capability is needed.
metadata:
  author: 101ta28
---

# vc-core

ローカル動画解析・編集パイプライン（vc）の基礎操作を行うためのスキルです。

## このスキルを使うタイミング

- ユーザーが「動画の解析をしたい」「render.json を作りたい」と言った
- ユーザーが `vc run` の使い方を求めている
- プロジェクトに `apps/cli/index.ts` が存在する
- `work/` や `outputs/` を生成したい

---

## 重要: 生成物と責務

- `transcript_segments.json` は解析層が生成する（`packages/analyze`）
- `render.json` はメタ層が生成する（`packages/meta`）
- `vc run` は両者の統合パイプラインとして扱う

---

## プロジェクト構成（vc）

```
apps/
  cli/                # vc CLI
  remotion/           # Remotion 描画
packages/
  analyze/            # 解析・STT
  meta/               # render.json 生成
  plan/               # LLM 編集プラン生成
  schema/             # 型定義
work/                 # 中間成果物
outputs/              # 出力先
```

---

## コマンド

```bash
bun run build
bun run vc run --in work/source.mp4 --prompt "結論だけ"
```

### オプション

- `--aspect 16:9|9:16`: 縦/横どちらかだけ出力したい場合に指定する
- `--out-dir <dir>`: 出力ディレクトリ（デフォルト `outputs`）
- `--plan <path>`: `plan.yaml` を指定して反映
- `--slug <name>`: Remotion 用の出力スラッグ（既定はファイル名）
- `--trim-source`: plan.yaml のクリップ範囲だけ切り出した source_trim.mp4 を使う

---

## 生成物の要点

- `work/<動画ファイル名>/transcript_segments.json`
  - `segments[]` に `startSec` / `endSec` / `text` / `textNorm`
- `work/<動画ファイル名>/render.json`
  - `outputs[]` に `horizontal` と `vertical` の出力
- `work/<slug>/render.json`（Remotion が `--public-dir work` で参照）

---

## 失敗時の対応

- `work/` を削除して再実行してよい
- `ffmpeg` が無い場合は `VC_AUDIO_PATH` を指定
- STT 失敗時は環境変数設定を再確認
 - 辞書を更新した後は `vc proofread` を使うと再解析せずに反映できる

---

## Codex/Claude での運用ルール

- ユーザーが「動画の解析をしたい」と言ったら、コマンド提示ではなく自動で `vc analyze` を実行する。
- `--prompt` が指定されていない場合は `VC_DEFAULT_PROMPT` を使い、未設定なら「要点をまとめて」を使う。
- STT 設定が不足している場合のみ、どの方式を使うか質問する。
- 辞書更新後の再処理は `vc proofread` を自動実行する。

---

## 固有名詞辞書の更新（JSON）

辞書は JSON で編集する。スキル適用時に必要なら自動で更新する。

- 全体辞書: `config/dictionary.json`
- 動画固有辞書: `work/<動画ファイル名>/dictionary.json`

更新時の注意:

- `wrongPatterns` は配列で列挙する
- 同じ誤変換は動画固有辞書が優先される
 - 動画固有辞書は解析時に空テンプレートが自動生成される
 - STT は OpenAI API を前提とし、辞書の `correct` をプロンプトに自動追加する

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/101ta28) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

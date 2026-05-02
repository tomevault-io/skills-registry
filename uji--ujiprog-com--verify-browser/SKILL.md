---
name: verify-browser
description: ローカル開発サーバーでの動作確認。HTML/CSS/JSの変更後、APIエンドポイント追加後、UIの見た目を確認したい時に使用。「ブラウザで確認」「動作確認して」「表示を確認」などのリクエストで発動。 Use when this capability is needed.
metadata:
  author: uji
---

# ブラウザ確認

ローカル開発サーバーを起動し、変更内容をブラウザで確認する。
agent-browser コマンド (https://github.com/vercel-labs/agent-browser) が必要。

## 手順

1. `make run` をバックグラウンドで実行し、localhost の URLが出力されるのを待つ
2. 10秒待機後、`agent-browser open {URL}` で出力されたURLを開く
3. 必要に応じて `agent-browser open {URL}` ブラウザを操作する
4. `agent-browser snapshot -i` でスナップショットを確認

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/uji) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

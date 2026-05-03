---
name: hello-world
description: Pythonスクリプトを使ってユーザーに挨拶をするサンプルスキルです。Antigravityカスタムスキルの構造を示すためのデモです。 Use when this capability is needed.
metadata:
  author: s-renren
---

# Instructions

ユーザーが挨拶を求めた場合、またはカスタムスキルシステムのテストを求めた場合、以下の手順に従ってください：

1.  **スクリプトの存在確認:** このスキルディレクトリ内に `scripts/hello.py` が存在するか確認してください。
2.  **スクリプトの実行:** `run_command` ツールを使用して Python スクリプトを実行してください。
    - コマンド: `python3 .agent/skills/hello_world/scripts/hello.py`
3.  **出力の報告:** コマンドの出力を読み取り、ユーザーに親しみやすいメッセージと共に報告してください。

もしスクリプトの実行に失敗した場合は、問題をデバッグ（例：ファイル権限やPythonのインストール確認など）して、再試行してください。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/s-renren) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

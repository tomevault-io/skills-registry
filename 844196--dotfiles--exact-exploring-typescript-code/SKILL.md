---
name: exploring-typescript-code
description: TypeScriptプロジェクトにおけるコード探索のベストプラクティス。LSPツールを優先的に使用し、型解決に基づく正確な結果を得る。 Use when this capability is needed.
metadata:
  author: 844196
---

# Exploring TypeScript Code

TypeScriptプロジェクトでは、以下のコード探索にLSPツールを優先的に使用する (Grep/Glob/Search(pattern:...)より型解決に基づく正確な結果が得られるため):

- `goToDefinition` / `goToImplementation`: シンボルの定義元・インターフェースの実装先を辿る
- `findReferences`: 特定シンボルへの参照を検索する（同名別シンボルの偽陽性を排除できる）
- `hover`: 推論された型情報やコメント・ドキュメントを確認する
- `incomingCalls` / `outgoingCalls`: 関数の呼び出し関係を把握する
- `documentSymbol`: ファイル内のシンボル構造を素早く把握する

ただし、テキストパターンの検索や、ワークスペース全体のシンボル列挙にはGrep/Globを使用する (`workspaceSymbol` は結果が限定的なため)。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/844196) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

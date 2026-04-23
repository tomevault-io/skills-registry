---
name: coding-guidelines
description: > Use when this capability is needed.
metadata:
  author: yurake
---

# Coding Guidelines Skill

チーム開発の設計・実装・テストで使うチェックリストを提示する。静的型付けOOP前提だが言語汎用。星は優先度: ★3=必ず守る、★2=強く推奨、★1=推奨、★0=参考。

## 使い方
- `mode`: `plan`（デフォルト）| `gate` を指定。
  - plan: 実装前の設計・方針確認用チェックリストを出す。
  - gate: 実装/テスト後のゲート判定。重大度高→低で指摘を返す。
- 入力ヒント
  - plan: 目的、非機能要件、対象言語/フレームワーク、リスク/制約。
  - gate: 変更ファイル/主要差分、既知の制約、テスト実行結果。
- 出力フォーマット
  - plan: ★3→★2→★1→★0 の順で1行1項目の短いリスト。
  - gate: 「章 / 状態(OK|Warn|Fail) / 短評 / 推奨修正」。★3違反はFail、★2はWarn目安。
- 章構成と詳細は references を参照する（必要なものだけ読む）。

## 章リンク
- 可用性: [references/availability.md](references/availability.md)
- 例外・エラー: [references/exceptions.md](references/exceptions.md)
- 共通化・構造: [references/commonality.md](references/commonality.md)
- 設計スケッチ: [references/design-sketch.md](references/design-sketch.md)
- コメント: [references/comments.md](references/comments.md)
- テスト: [references/testing.md](references/testing.md)
- 数値計算: [references/math.md](references/math.md)

## 参考メモ
- 言語固有の補足（Javaのチェック例外、Swiftプロトコル→クロージャ置換など）は各章の末尾に置いている。必要なときだけ参照。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yurake) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

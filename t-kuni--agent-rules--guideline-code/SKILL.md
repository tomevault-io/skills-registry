---
name: guideline-code
description: コーディングガイドライン。参照条件： コード・実装(*.go)を作成・編集する Use when this capability is needed.
metadata:
  author: t-kuni
---


# コーディングガイドライン

* コメントは日本語で記述する
* コードを書く際は同一レイヤの類似のファイルを参考にすること
* パブリック関数はモジュール毎に１つが望ましい
  * 他のモジュールから呼び出さない関数はプライベート関数とする
* 構造体の受け渡しは特別な理由が無い限り値渡しとする
* エラーをreturnする場合は `fmt.Errorf("error messeage: %w", err)` でラップする

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/t-kuni) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

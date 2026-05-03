---
name: ts-code-review
description: コードをレビューする。「このコード見て」「レビューして」と言われた時に使用。 Use when this capability is needed.
metadata:
  author: nozomi-iida
---

# コードレビュースキル

TypeScript/JavaScriptのコードを対象に、コード品質・セキュリティ・パフォーマンスの観点でレビューを行う。

## レビュー観点

### コード品質
- 可読性: 変数名・関数名は意図が明確か
- 重複: 同じロジックが複数箇所にないか
- 関数の責務: 1つの関数が複数の責務を持っていないか
- エラーハンドリング: try-catchが適切か、エラーが握りつぶされていないか
- 型安全性: any型の乱用がないか、適切な型定義がされているか

### セキュリティ
- XSS: dangerouslySetInnerHTMLの使用、ユーザー入力の直接埋め込み
- インジェクション: SQLインジェクション、コマンドインジェクションのリスク
- 機密情報: APIキー、パスワード、トークンのハードコード
- 認証・認可: 権限チェックの漏れ

### パフォーマンス
- 不要な再レンダリング: useCallback, useMemo, React.memoの適切な使用
- メモリリーク: useEffectのクリーンアップ漏れ、イベントリスナーの解除忘れ
- N+1問題: ループ内での非同期処理、APIコール
- バンドルサイズ: 不要なimport、大きなライブラリの全体import

## 出力形式

各ファイルごとに以下の形式でレビュー結果を出力:

```
## ファイル名

### 問題点
- [重要度: 高/中/低] 問題の説明
  - 該当箇所: `コード片`
  - 理由: なぜ問題なのか
  - 改善案: どう修正すべきか

### 良い点
- 良かった点があれば記載
```

問題がない場合は「特に問題なし」と記載する。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nozomi-iida) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

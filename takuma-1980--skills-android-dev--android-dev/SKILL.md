---
name: android-dev
description: Android UI実装・API/Repository設計スキル。Jetpack Compose によるUI構築、Retrofit/Room によるデータ層の設計・実装を支援します。Jetpack ComposeによるUI構築や、Retrofit/Roomを用いたデータ層の設計・実装を行う際に使用してください。トリガー：「Compose」「Kotlin Android」「Android実装」「Retrofit」「Room」「UI実装」 Use when this capability is needed.
metadata:
  author: takuma-1980
---

# Android Development Skill

# Instructions
あなたは熟練したAndroidデベロッパーです。以下の技術スタックとガイドラインに従い、高品質で保守性の高いKotlinコードを設計・実装してください。

## 1. 技術スタック
実装は常に以下のスタックで行ってください。Javaは使用せず、モダンなKotlin記述を用いてください。

| カテゴリ | 技術 |
|---|---|
| 言語 | Kotlin |
| UI | Jetpack Compose + Material 3 |
| アーキテクチャ | MVVM (ViewModel + StateFlow) |
| DI | Hilt |
| ネットワーク | Retrofit + OkHttp + Moshi/kotlinx.serialization |
| ローカルDB | Room |
| 非同期処理 | Kotlin Coroutines + Flow |
| 画像 | Coil |

## 2. 実装ワークフロー

ユーザーの依頼内容に応じて、適切な実装方針を選択してください。

### A. UI実装 (Jetpack Compose)
画面やコンポーネントの実装を求められた場合：
1.  **Stateless化:** 可能な限りステートレスなComposableを作成し、State Hoistingを行ってください。
2.  **State管理:** 画面の状態は `UiState` (data class/sealed interface) で定義し、ViewModelの `StateFlow` で管理してください。
3.  **Preview:** 実装コードには必ず `@Preview` を含めてください。

詳細なUIパターンは必要に応じて [references/ui-patterns.md](references/ui-patterns.md) を参照・提示してください。

### B. API/Repository実装
データ取得や保存ロジックを求められた場合：
1.  **Repositoryパターン:** データソース（API/DB）を直接ViewModelから呼ばず、Repositoryで隠蔽してください。
2.  **エラーハンドリング:** `runCatching` や `Result` 型を使用し、例外でアプリをクラッシュさせないようにしてください。
3.  **Offline-first:** Roomを使用したキャッシュ戦略を考慮してください。

詳細な設計パターンは [references/api-repository.md](references/api-repository.md) を参照してください。

## 3. コーディング品質基準 (Important)
- **非推奨APIの回避:** `findViewById` や `Synthetics` は使用禁止です。
- **コメント:** 複雑なロジックにはKDoc形式のコメントを付与してください。
- **リソース:** 文字列やサイズはハードコードせず、可能な限りリソースファイル (`strings.xml`, `dimens.xml`) の使用を前提としてください。

# Examples

**User:** 「ログイン画面のUIを作って。ユーザー名とパスワード入力、ログインボタンが必要。」
**Action:** `references/ui-patterns.md` の入力フォームのベストプラクティスに基づき、`LoginScreen` (Stateless) と `LoginRoute` (Stateful) を分離したJetpack Composeのコードを提示する。

**User:** 「ユーザー一覧を取得するAPI連携の実装をお願い。」
**Action:** Retrofitのインターフェース定義、データクラス、そしてそれを呼び出すRepositoryの実装（Flowでデータを返す形）を提示する。エラーハンドリング（通信エラー時の処理）を含める。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/takuma-1980) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

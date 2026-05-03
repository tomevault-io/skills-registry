---
name: tdd-architect-scribe
description: Use this skill when the user is developing software, writing code, refactoring, or setting up a development environment. It enforces Test Driven Development (TDD) cycles and logs progress.
metadata:
  author: noncha-ts023
---

# TDD Architect & Scribe

## 説明
テスト駆動開発（TDD）のサイクル（Red -> Green -> Refactor）を厳格に遵守させ、同時に開発の軌跡を詳細なログとして記録するスキルです。
言語やフレームワークを問わず、実装コードを書く前にテストコードの存在を必須とし、開発プロセスそのものをドキュメント化します。

## アクション手順

### Phase 1: Test First (Red)
1.  **要件の確認**: 実装する機能の要件を入出力（Input/Output）レベルで明確にします。
2.  **テスト作成**: 実装コードを書く**前に**、必ず失敗するテストケースを作成します。
    - 対象: ロジックの境界値、異常系、UIの期待される挙動など。
3.  **確認**: テストを実行し、意図通りに失敗（Red）することを確認します。

### Phase 2: Implementation (Green)
1.  **最小実装**: テストを通過させるための必要最小限のコードを実装します。
2.  **検証**: テストが通過（Green）することを確認します。

### Phase 3: Logging (The Scribe)
開発の節目（テスト作成時、実装完了時、エラー解決時）に、自動的に以下のフォーマットで `docs/development_log.md` に追記します。
（ファイルが存在しない場合は作成します）

- **Timestamp**: (現在時刻)
- **Phase**: [TDD-Red / TDD-Green / Refactor / Environment-Setup]
- **Change**: (行った変更の要約)
- **Reason**: (なぜその変更が必要だったか、技術的な選定理由)
- **Next**: (次に行うべきステップ)

## 制約事項
- テストコードが存在しない機能の実装コードを提案してはいけません。
- 環境構築の手順（0から1へ）も、実行コマンドと共にログに記録してください。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/noncha-ts023) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

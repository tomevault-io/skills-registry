---
name: scenario-test-generator
description: アプリケーション向けのUX重視のシナリオベーステストケースを生成します。 Use when this capability is needed.
metadata:
  author: tnkmr32
---

# シナリオテストジェネレーター（UX 重視）

## このスキルを使用するタイミング

このスキルは、ユーザーが以下を必要とする場合に使用します：

- アプリケーションの包括的な UX シナリオテストケースを作成
- ユーザージャーニーとワークフローの検証
- 異なるユースケースにおけるユーザー体験のテスト
- 期待されるユーザー行動とシステムレスポンスの文書化
- アプリケーションがユーザー体験要件を満たしていることの確認

## 仕組み

このスキルは、以下に焦点を当てた構造化されたテストケースを生成します：

1. **ユーザーゴール**: ユーザーが達成しようとしていること
2. **ユーザージャーニー**: ユーザーが取るステップバイステップのアクション
3. **期待される結果**: 各ステップで何が起こるべきか
4. **UX 検証**: ユーザビリティ、アクセシビリティ、ユーザー満足度の基準

## 出力形式

テストケースは、[assets/test-case-template.md](assets/test-case-template.md) のテンプレートに従って生成されます。

## 出力先

生成されたテストケースは、`output/scenario-test/` ディレクトリに保存されます。
ファイル名は `TC-<シーケンシャル番号>-<簡潔な説明>.md` 形式になります。
例: `TC-001-add-first-task.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tnkmr32) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

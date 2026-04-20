---
name: creating-issues
description: GitHub Issueを作成し、適切なラベルとマイルストーンを設定します。Issue作成、タスク登録、バグ報告を求められた場合に使用してください。 Use when this capability is needed.
metadata:
  author: tqer39
---

# GitHub Issue 作成

## 重要ルール

**Issue は PR のマージによってのみクローズする。**

- `gh issue close` コマンド使用禁止
- PR 本文に `Closes #123` を記載してマージでクローズ

## 実行手順

1. タイトル: emoji prefix + 簡潔で具体的に（日本語）
2. ラベル: `backend`, `frontend`, `infra`, `docs`, `bug`, `enhancement` から**必ず1つ以上選択**
3. マイルストーン: `MVP` または `v1.0` を**必ず設定**
4. `gh issue create --label <label> --milestone <milestone>` で作成

> **重要**: ラベルとマイルストーンは必須。省略しないこと。

## Issue Title 形式

<!-- markdownlint-disable MD060 -->

| タイプ         | Emoji | 例                                  |
|----------------|-------|-------------------------------------|
| 機能追加       | ✨    | `✨ キャラクター設定機能を追加`    |
| バグ           | 🐛    | `🐛 音声再生でエラーが発生`        |
| ドキュメント   | 📝    | `📝 セットアップガイドを追加`      |
| インフラ       | 🔧    | `🔧 CI/CD パイプラインを構築`      |
| テスト         | ✅    | `✅ E2E テストを追加`              |
| パフォーマンス | ⚡    | `⚡ LLM レスポンス速度を改善`      |

<!-- markdownlint-enable MD060 -->

## Issue テンプレート

```markdown
## 概要
[この Issue で何を達成するか]

## タスク
- [ ] タスク1
- [ ] タスク2

## 実行コマンド
\`\`\`bash
# 具体的なコマンドを記載
\`\`\`

## 作成/変更されるファイル
- `path/to/new-file` (新規作成)
- `path/to/modified-file` (変更)

## 検証手順
\`\`\`bash
# 検証用コマンド
\`\`\`

## 関連ファイル
- `path/to/file1`
```

## Issue 作成時の必須項目

### 1. 実行コマンドを明記

❌ 悪い例:「Style-Bert-VITS2をsubmoduleとして追加」

✅ 良い例:

```bash
git submodule add https://github.com/tqer39/Style-Bert-VITS2.git Style-Bert-VITS2
```

### 2. 作成/変更されるファイルを明記

❌ 悪い例:「設定ファイルを更新」

✅ 良い例:

作成されるファイル:

- `.gitmodules`
- `Style-Bert-VITS2/` (submodule)

### 3. 検証手順を明記

❌ 悪い例:「動作確認する」

✅ 良い例:

```bash
git submodule update --init --recursive
ls Style-Bert-VITS2/  # ファイルが存在することを確認
```

## ラベル選択ガイド

| ラベル        | 用途                         |
|---------------|------------------------------|
| `backend`     | FastAPI、Python、API 関連    |
| `frontend`    | React、TypeScript、UI 関連   |
| `infra`       | CI/CD、Docker、設定ファイル  |
| `docs`        | ドキュメント                 |
| `bug`         | バグ修正                     |
| `enhancement` | 新機能・改善                 |

## マイルストーン

| マイルストーン | 用途                       |
|----------------|----------------------------|
| `MVP`          | 最小動作に必要な機能       |
| `v1.0`         | 基本機能完成に必要な機能   |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tqer39) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

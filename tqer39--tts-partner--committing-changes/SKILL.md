---
name: committing-changes
description: Gitの変更をコミットし、適切なemoji prefix付きのコミットメッセージを作成します。コミット、変更の保存、git commitを求められた場合に使用してください。 Use when this capability is needed.
metadata:
  author: tqer39
---

# Git Commit 作成

## 実行手順

1. `git status` で変更内容を確認
2. `git diff` で詳細な変更を確認
3. 変更内容に基づいて適切な type と emoji を選択
4. コミットメッセージを作成
5. `git commit` を実行

## Commit Message 形式

```text
<emoji> <type>: <subject>

<body>

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>
```

## Type と Emoji 対応表

| Type       | Emoji | 用途                   |
|------------|-------|------------------------|
| `feat`     | ✨    | 新機能                 |
| `fix`      | 🐛    | バグ修正               |
| `docs`     | 📝    | ドキュメント           |
| `style`    | 💄    | スタイル・フォーマット |
| `refactor` | ♻️    | リファクタリング       |
| `test`     | ✅    | テスト追加・修正       |
| `chore`    | 🔧    | 設定・ビルド関連       |
| `perf`     | ⚡    | パフォーマンス改善     |
| `ci`       | 👷    | CI/CD                  |
| `build`    | 📦    | ビルド・依存関係       |
| `revert`   | ⏪    | リバート               |
| `wip`      | 🚧    | 作業中                 |
| `init`     | 🎉    | 初期化                 |
| `security` | 🔒    | セキュリティ           |
| `breaking` | 💥    | 破壊的変更             |

## 例

```bash
git commit -m "$(cat <<'EOF'
✨ feat: キャラクター設定機能を追加

- キャラクター設定ファイルの読み込み
- キャラクター切り替え API

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>
EOF
)"
```

## 注意事項

- 1コミット1目的
- 現在形で記述（日本語なら「追加」）
- subject は50文字以内
- Co-Authored-By は必須

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tqer39) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

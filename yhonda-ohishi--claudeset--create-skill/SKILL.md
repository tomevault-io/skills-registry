---
name: create-skill
description: 新しいClaude Codeスキルを作成します Use when this capability is needed.
metadata:
  author: yhonda-ohishi
---

$ARGUMENTS という名前の新しいスキルを作成します。

## 手順

1. `.claude/skills/$ARGUMENTS/SKILL.md` を作成
2. ユーザーにスキルの説明と用途を確認
3. 適切なテンプレートを生成

## テンプレート

```yaml
---
name: <skill-name>
description: スキルの説明（Claudeが自動実行するかどうかの判断に使用）
disable-model-invocation: true  # 手動呼び出しのみにする場合
argument-hint: <引数のヒント>    # オートコンプリート用
---

$ARGUMENTS を使って処理を行います：

1. ステップ1
2. ステップ2
3. ステップ3
```

## ベストプラクティス

### フォルダ構成
```
.claude/skills/<skill-name>/
├── SKILL.md              # 必須: メイン手順ファイル
├── reference.md          # 詳細なAPI仕様（オプション）
├── examples/
│   └── sample.md         # 出力サンプル（オプション）
└── scripts/
    └── validate.sh       # 実行可能スクリプト（オプション）
```

### Frontmatter オプション

| フィールド | 説明 |
|-----------|------|
| `name` | スキル名（省略時はディレクトリ名） |
| `description` | スキルの説明（推奨） |
| `disable-model-invocation` | `true` で自動実行を禁止（手動のみ） |
| `user-invocable` | `false` でユーザー呼び出し不可 |
| `allowed-tools` | 使用可能なツールを限定（例：`Read, Grep`） |
| `argument-hint` | オートコンプリートのヒント |
| `context` | `fork` で独立した subagent で実行 |
| `agent` | 使用する subagent タイプ（`Explore`, `Plan` など） |
| `model` | 使用するモデルを指定 |

### 重要なポイント

1. **Description は明確に** - Claudeが自動実行するかどうかを判断するため
2. **SKILL.md は簡潔に** - 500行以下が目安。詳細は別ファイルに
3. **$ARGUMENTS を活用** - ユーザーからの引数を受け取る
4. **!`command`** - シェルコマンドの出力を動的に埋め込み可能

### 動的コンテキストの例

```yaml
---
name: pr-summary
context: fork
agent: Explore
---

## PR コンテキスト
- PR diff: !`gh pr diff`
- 変更ファイル: !`gh pr diff --name-only`

このPRを要約してください...
```

## 作成後の確認

- `/skills` で一覧を確認
- `/<skill-name>` で直接呼び出しをテスト

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yhonda-ohishi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

---
name: continuous-learning
description: Claude Code セッションから再利用可能なパターンを自動的に抽出し、学習済みスキルとして保存します Use when this capability is needed.
metadata:
  author: rx-k8
---

# 継続学習スキル

Claude Code セッションを終了時に自動的に評価し、学習済みスキルとして保存できる再利用可能なパターンを抽出します。

## 仕組み

このスキルは各セッションの終了時に **Stop フック**として実行されます:

1. **セッション評価**: セッションに十分なメッセージがあるかチェック（デフォルト: 10 以上）
2. **パターン検出**: セッションから抽出可能なパターンを識別
3. **スキル抽出**: 有用なパターンを `~/.claude/skills/learned/` に保存

## 設定

`config.json` を編集してカスタマイズ:

```json
{
  "min_session_length": 10,
  "extraction_threshold": "medium",
  "auto_approve": false,
  "learned_skills_path": "~/.claude/skills/learned/",
  "patterns_to_detect": [
    "error_resolution",
    "user_corrections",
    "workarounds",
    "debugging_techniques",
    "project_specific"
  ],
  "ignore_patterns": [
    "simple_typos",
    "one_time_fixes",
    "external_api_issues"
  ]
}
```

## パターンタイプ

| パターン | 説明 |
|---------|-------------|
| `error_resolution` | 特定のエラーがどのように解決されたか |
| `user_corrections` | ユーザーの修正からのパターン |
| `workarounds` | フレームワーク/ライブラリの癖への解決策 |
| `debugging_techniques` | 効果的なデバッグアプローチ |
| `project_specific` | プロジェクト固有の規約 |

## フック設定

`~/.claude/settings.json` に追加:

```json
{
  "hooks": {
    "Stop": [{
      "matcher": "*",
      "hooks": [{
        "type": "command",
        "command": "~/.claude/skills/continuous-learning/evaluate-session.sh"
      }]
    }]
  }
}
```

## なぜ Stop フック？

- **軽量**: セッション終了時に一度だけ実行
- **ノンブロッキング**: すべてのメッセージにレイテンシを追加しない
- **完全なコンテキスト**: セッショントランスクリプト全体にアクセス可能

## 関連

- [The Longform Guide](https://x.com/affaanmustafa/status/2014040193557471352) - 継続学習のセクション
- `/learn` コマンド - セッション途中での手動パターン抽出

---

## 比較メモ（調査: 2025 年 1 月）

### vs Homunculus (github.com/humanplane/homunculus)

Homunculus v2 はより洗練されたアプローチを取ります:

| 機能 | 我々のアプローチ | Homunculus v2 |
|---------|--------------|---------------|
| 観察 | Stop フック（セッション終了時） | PreToolUse/PostToolUse フック（100% 信頼性） |
| 分析 | メインコンテキスト | バックグラウンドエージェント（Haiku） |
| 粒度 | 完全なスキル | 原子的「本能」 |
| 信頼度 | なし | 0.3-0.9 の重み付け |
| 進化 | スキルへ直接 | 本能 → クラスター → スキル/コマンド/エージェント |
| 共有 | なし | 本能のエクスポート/インポート |

**homunculus からの重要な洞察:**
> "v1 はスキルに依存して観察していました。スキルは確率的で、約 50-80% の確率で起動します。v2 は観察にフック（100% 信頼性）を使用し、本能を学習された動作の原子単位として使用します。"

### 潜在的な v2 拡張

1. **本能ベースの学習** - 信頼度スコア付きの小さな原子的動作
2. **バックグラウンドオブザーバー** - 並行して分析する Haiku エージェント
3. **信頼度減衰** - 矛盾すると本能の信頼度が低下
4. **ドメインタグ付け** - code-style、testing、git、debugging など
5. **進化パス** - 関連する本能をスキル/コマンドにクラスター化

詳細は `/Users/affoon/Documents/tasks/12-continuous-learning-v2.md` を参照。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rx-k8) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

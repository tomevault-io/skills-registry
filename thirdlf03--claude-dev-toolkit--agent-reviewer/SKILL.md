---
name: agent-reviewer
description: Claude Code Subagentsのレビューと品質改善。ベストプラクティスに基づいてSubagentを分析し、評価レポートと改善提案を提供。"review agent", "agentをレビュー", "subagent品質チェック" で起動。 Use when this capability is needed.
metadata:
  author: thirdlf03
---

# Agent Reviewer

Subagentの品質レビューと改善提案を行います。

## Prerequisites

**Required subagent**:
- `.claude/agents/agent-reviewer.md` - Subagentレビューを実行

## Usage

```
/agent-reviewer [agent-name or path]
```

例:
- `/agent-reviewer deep-researcher`
- `/agent-reviewer .claude/agents/parallel-researcher.md`
- `/agent-reviewer --all`

## Workflow

### 1. 対象Agentの特定

**引数あり**: 指定されたエージェントをレビュー
```
/agent-reviewer deep-researcher
→ .claude/agents/deep-researcher.md をレビュー
```

**引数なし**: 対話的に選択
```
/agent-reviewer
→ 利用可能なエージェント一覧を表示:
   1. deep-researcher
   2. parallel-researcher
   3. repo-analyzer
   ...
→ ユーザーに選択を求める
```

**--all オプション**: 全エージェントをレビュー
```
/agent-reviewer --all
→ .claude/agents/*.md を全て並列レビュー
```

### 2. agent-reviewer subagent を起動

```
Task(
  subagent_type: "agent-reviewer",
  prompt: "[agent-path] のSubagentをレビューしてください。評価レポートと改善提案を出力してください。"
)
```

### 3. 結果をユーザーに提示

## Review Criteria

### Frontmatter (必須)
| Field | Required | Validation |
|-------|----------|------------|
| name | Yes | lowercase-hyphen, 64文字以下 |
| description | Yes | WHAT + WHEN, 1024文字以下 |
| tools | Recommended | 明示的な配列または省略 |
| permissionMode | Optional | default/acceptEdits/dontAsk/bypassPermissions |
| model | Optional | sonnet/opus/haiku |
| color | Optional | terminal color name |

### Subagent特有の基準
| Criterion | Good | Bad |
|-----------|------|-----|
| Single Responsibility | 1つの明確な目的 | 複数の無関係なワークフロー |
| Tool Scoping | 必要最小限のtools | tools省略（全ツール許可） |
| Permission Mode | default（安全） | bypassPermissions（危険） |
| Prompt Length | 100-500行 | 500行超（Over-Specified） |
| Output Format | 明確に定義 | 曖昧または未定義 |
| Error Handling | 具体的なシナリオ | 記載なし |

## Options

| Option | Description | Status |
|--------|-------------|--------|
| `--fix` | 自動修正を適用 | 実装済み |
| `--all` | 全Agentをレビュー | 実装済み |
| `--score-only` | スコアのみ表示 | 実装済み |

## Error Handling

| Condition | Behavior |
|-----------|----------|
| Agent not found | エラーメッセージ + 利用可能なエージェント一覧を表示 |
| subagent missing | エラー報告 + subagent作成を提案 |
| Frontmatter parse error | YAMLエラー箇所を特定して報告 |
| --all で一部失敗 | 成功したものは結果表示、失敗したものはエラー報告 |

## Output Format

```
📋 Agent Review: [agent-name]

✅ 良い点
- ...

⚠️ 改善提案
1. [問題] → [解決策]

📊 適合度スコア: ⭐⭐⭐⭐☆ (4/5)

🎯 推奨アクション
1. [優先度高] ...
2. [優先度中] ...
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thirdlf03) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

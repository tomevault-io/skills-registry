---
name: gemini-cli
description: | Use when this capability is needed.
metadata:
  author: P4sTela
---

# Gemini CLI Skill

Gemini CLIを使用して、Claudeの機能を補完するタスクを実行する。

## When to Use

| ユースケース | トリガー例 |
|-------------|-----------|
| マルチモーダル | 「この画像を分析」「音声を文字起こし」「動画の内容を説明」 |
| 大規模分析 | 「プロジェクト全体を分析」「このディレクトリを見て」 |
| Web検索 | 「Geminiで最新情報を調べて」「〇〇についてリサーチ」 |
| 相談・レビュー | 「Geminiに計画をレビューしてもらって」「この実装方針を相談」 |

## Rate Limit Awareness

Gemini Proプランでは無料枠より多くのリクエストが可能だが、無制限ではない。

- 連続で大量実行は避ける
- 軽微な質問や確認にはClaudeを使う
- 大規模分析は必要なときだけ

## Execution

### 基本コマンド

```bash
cd <target-directory>
npx @google/gemini-cli -y "<prompt>"
```

**必須オプション**:
- `-y` / `--yolo`: 非対話モード（必須、ないとハングする）

**便利なオプション**:
- `-o json`: JSON形式で出力
- `-m gemini-2.0-flash-thinking-exp`: 深い推論が必要な場合
- `--include-directories <dir1>,<dir2>`: 特定ディレクトリのみ解析

### パターン別実行例

**1. マルチモーダル分析**
```bash
cd ~/path/to/files
npx @google/gemini-cli -y "screenshot.pngのUIを評価してください"
npx @google/gemini-cli -y "meeting.mp3を文字起こしして議事録形式で"
npx @google/gemini-cli -y "demo.mp4の内容を要約してください"
```

**2. プロジェクト分析（100万トークン活用）**
```bash
cd /path/to/project
npx @google/gemini-cli -y "このプロジェクトのアーキテクチャを分析してください"
```

**3. Web検索**
```bash
npx @google/gemini-cli -y "2026年のRust最新動向を調べてください"
```

**4. 相談・レビュー**
```bash
npx @google/gemini-cli -y "以下の実装計画をレビューしてください: <計画内容>"
```

### 長いプロンプト（Heredoc）

```bash
npx @google/gemini-cli -y "$(cat <<'EOF'
以下の計画をレビューしてください：

## 目的
...

## 実装方針
...

## 懸念点
...
EOF
)"
```

## Workflow for Review/Consultation

相談・レビュー依頼時の推奨フロー：

1. **コンテキスト整理**: 相談内容を明確に構造化
2. **Gemini実行**: 適切なプロンプトでレビュー依頼
3. **結果評価**: Geminiの指摘を鵜呑みにせず、根拠を確認
4. **総合判断**: 両者の視点を比較し、最終判断は自分で下す

**注意**: Geminiの提案を無条件に採用しない。異なる視点からの意見として参考にする。

## Detailed References

- **[capabilities.md](references/capabilities.md)** - 対応ファイル形式、制限値の詳細
- **[examples.md](references/examples.md)** - 実践的な使用例集
- **[troubleshooting.md](references/troubleshooting.md)** - エラー対処法

---
> Source: [P4sTela/claude-skills](https://github.com/P4sTela/claude-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->

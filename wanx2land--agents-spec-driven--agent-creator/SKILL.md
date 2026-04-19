---
name: agent-creator
description: >- Use when this capability is needed.
metadata:
  author: wanx2land
---

# Agent Creator

`.claude/agents/` 配下にエージェント定義ファイルを作成するスキル。
公式の Agent Creation Architect パターンに準拠する。

**IMPORTANT**: CLAUDE.md が存在する場合、エージェント作成前に必ず読み込み、
プロジェクト固有のコーディング規約、構造、カスタム要件を設計に反映させる。

## Key Principles（公式ガイドライン準拠）

1. **Be specific rather than generic** - 曖昧な指示を避け、具体的に記述する
2. **Include concrete examples** - 動作を明確化する `<example>` を含める
3. **Balance comprehensiveness with clarity** - すべての指示が価値を追加すべき
4. **Build in quality assurance mechanisms** - 自己検証と品質管理のステップを組み込む

## ワークフロー（公式 6 ステップ準拠）

### 1. Extract Core Intent（コア意図の抽出）

ユーザーの要件から以下を特定する:

- **基本的な目的**: エージェントの根本的な存在理由
- **主要な責任**: 何を専門とするか
- **成功基準**: 何をもって成功とするか
- **暗黙のニーズ**: 明示されていない要件

**コードレビュー系エージェント**: 明示的な指示がない限り、
コードベース全体ではなく最近書かれたコードのレビューを想定する。

既存エージェントの更新の場合は、対象ファイルを読み込み変更点を確認する。

### 2. Design Expert Persona（専門家ペルソナの設計）

タスクに関連する深いドメイン知識を体現する説得力のある
専門家アイデンティティを作成する。
ペルソナはエージェントの意思決定アプローチを導くものとする。

例: "You are an elite security auditor specializing in..."

### 3. 既存エージェントの調査

`.claude/agents/` 内の既存エージェントを Glob で確認し、
命名規則・構造パターン・モデル選択の傾向を把握する。
エージェント設計の詳細パターンは
[references/agent-patterns.md](references/agent-patterns.md) を参照する。

### 4. Architect Comprehensive Instructions（包括的指示の設計）

システムプロンプトを設計する際、以下を含める:

- **明確な動作境界と運用パラメータ**
- **タスク実行のための具体的な方法論とベストプラクティス**
- **エッジケースの予測と処理ガイダンス**
- **ユーザーが言及した特定の要件や好みの取り込み**
- **関連する場合の出力形式の期待値**
- **CLAUDE.md からのプロジェクト固有のコーディング規約との整合**

### 5. Optimize for Performance（パフォーマンス最適化）

以下を含める:

- **ドメインに適した意思決定フレームワーク**
- **品質管理メカニズムと自己検証ステップ**
- **効率的なワークフローパターン**
- **明確なエスカレーションまたはフォールバック戦略**

### 6. Create Identifier & Example Descriptions（識別子と例の作成）

識別子を設計する:

- **小文字、数字、ハイフンのみ使用**
- **通常 2-4 語をハイフンで結合**
- **エージェントの主要機能を明確に示す**
- **覚えやすく入力しやすい**
- **"helper" や "assistant" などの汎用用語を避ける**

`.claude/agents/<agent-name>.md` を以下の構造で作成する。

```markdown
---
name: <agent-name>
description: >-
  <役割の説明 + トリガー条件>
  <example>
  user: "<ユーザー発話例1>"
  assistant: [<起動時の動作>]
  </example>
  <example>
  user: "<ユーザー発話例2>"
  assistant: [<起動時の動作>]
  </example>
model: <sonnet|opus|inherit>
color: <色>
tools: >-
  <必要なツールのカンマ区切りリスト>
---

# 専門家ロール

<専門家としての宣言 + 専門領域>

# コアミッション

<このエージェントの目的を1-2文で>

# 入力の受け取り

<起動時のプロンプトから取得する情報>

# プロセス

## 1. <最初のステップ>
...

## 2. <次のステップ>
...

# 出力フォーマット

<生成する成果物の形式>

# スコープ外

- <このエージェントがやらないこと>

# 品質基準

- <成果物の品質チェック項目>
```

### 7. Frontmatter の設計ルール

**name**: 小文字ハイフンケース（例: `code-reviewer`, `spec-writer`）

**description**: トリガーメカニズムとして機能する（公式 whenToUse に相当）。
- **CRITICAL**: 役割の説明 + `<example>` ブロックでユーザー発話例を 2-3 個記述する
- `<example>` には `user:` と `assistant:` の両方を含める
- **プロアクティブ使用**: エージェントがユーザーの明示的なリクエストなしに
  起動すべき場合、その条件を description に明記する
  （例: "コードが書かれた後は自動的にテストを実行すべき"）
- **簡潔に保つ**: description は常にコンテキストに読み込まれるため、
  10行以内を目安に簡潔に記述する。冗長な説明は避け、
  役割の要約 + example 2個程度に抑える

**公式 example フォーマット**:
```
<example>
user: "Please write a function that checks if a number is prime"
assistant: "Here is the relevant function..."
<commentary>
Since a significant piece of code was written, use the Task tool to launch the test-runner agent.
</commentary>
assistant: "Now let me use the test-runner agent to run the tests"
</example>
```

**model** の選択基準:
- `sonnet`: 分析・設計・検証（多くのケースで採用）
- `opus`: 高度なレビュー・複雑な生成タスク
- `inherit`: 呼び出し元のモデルを継承（補助タスク向け）

**color** の割り当て:
- `blue`/`cyan`: 分析・レビュー
- `green`: 生成・作成
- `yellow`: 検証・注意
- `red`: セキュリティ/クリティカル
- `magenta`: 変換/創造的タスク

**tools**: 最小権限の原則で必要なツールのみ指定する。
未指定は全ツール利用可能。

### 8. 本文の設計ルール

- **専門家ロールの宣言**から始める
- **プロセスは番号付きステップ**で記述する
- **出力フォーマット**を具体的なテンプレートで示す
- **スコープ外**を明示してエージェントの境界を定める
- **品質基準**で成果物の検証項目を列挙する
- 曖昧な表現（「適切に」「必要に応じて」）を避け、具体的に記述する

### 9. 検証

バリデータースクリプトを実行して構造を検証する。

```bash
python3 .claude/skills/agent-creator/scripts/validate_agent.py .claude/agents/<agent-name>.md
```

スクリプトは以下を自動検証する:

- YAML frontmatter の構文と必須フィールド（name, description）
- name がファイル名と一致するか
- description に `<example>` ブロック（2個以上推奨）が含まれるか
- model が `sonnet`/`opus`/`inherit` のいずれかであるか
- tools が指定されているか（最小権限の原則）
- 本文に推奨セクション（専門家ロール、コアミッション、プロセス、出力フォーマット、品質基準）があるか
- プロセスセクションに番号付きステップがあるか
- スコープ外セクションがあるか

**ERROR は必ず修正する。WARNING もエラーと同等に扱い、正当な理由がない限りすべて修正する。**

ディレクトリ指定で全エージェントを一括検証することも可能:

```bash
python3 .claude/skills/agent-creator/scripts/validate_agent.py .claude/agents/
```

スクリプトの自動検証に加えて、以下を手動で確認する:

- [ ] 既存エージェントとの役割重複がない

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wanx2land) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

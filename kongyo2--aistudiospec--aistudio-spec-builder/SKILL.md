---
name: aistudio-spec-builder
description: Creates high-quality specification documents for Google AI Studio's app builder. This skill should be used when users want to create or refine specifications for React + TypeScript web applications that will be built using Google AI Studio's Build feature. The skill guides users through requirements gathering, functional decomposition, UI/UX design, and produces structured specifications without any code, using only natural language instructions.
metadata:
  author: kongyo2
---

# Google AI Studio App Builder Specification Creator

## Overview

Google AI Studio's app builder (Build feature) is a coding agent that automatically generates React + TypeScript web applications from natural language prompts. To maximize the effectiveness of this builder, it requires clear, structured specifications that describe application behavior without including actual code.

This skill transforms ambiguous user requirements into high-quality specifications that the app builder can interpret and implement effectively.

## When to Use This Skill

Use this skill when:
- Creating specifications for React + TypeScript web applications
- Building apps for Google AI Studio's app builder
- Converting natural language requirements into structured specifications
- Users mention "Google AI Studio", "app builder", or "Build feature"
- Users want to create a spec/specification for a web app

## Target System Constraints

All specifications created must adhere to Google AI Studio app builder constraints:
- **Development Target**: React + TypeScript web applications only
- **Project Structure**: Must include `index.html` and `index.tsx`
- **Development Process**: Natural language instructions generate code automatically
- **Prohibited**: Python server-side processing, frameworks other than React, any constraint violations

## Core Principles

### 1. No Code in Specifications
**CRITICAL**: Never include actual code (HTML, CSS, TypeScript, JSX) in specifications. Instead, write clear instructions describing system behavior. Think "instructions for the builder" not "implementation details."

### 2. User-Centric Approach
Prioritize user requests and provide maximum value within constraints. When requirements are unclear, ask clarifying questions rather than making assumptions.

### 3. Approval Process
**MANDATORY**: After every spec update, ask: "Specの内容は問題ないでしょうか?問題なければタスクを完了します。"

Do not complete the task until receiving explicit approval like:
- "yes" / "はい"
- "approved" / "承認"
- "looks good" / "問題ない"
- "OK"

### 4. Iterative Refinement
With each feedback cycle:
1. Clearly indicate what changes were made
2. Explain why the changes improve the spec
3. Present the updated spec
4. Request approval again

## Requirements Gathering Process

### Step 1: Initial Requirements
When receiving the first request, confirm these three axes:

1. **Purpose**: What problem does this app solve?
2. **Users**: Who will use it? (expertise level, usage scenarios)
3. **Core Features**: What features are absolutely necessary?

**Example dialogue**:
```
User: "Create a customer management tool"
Assistant: "I'll help you create a spec. Let me clarify a few points:
1. What customer information do you need to manage? (name, contact info, purchase history, etc.)
2. Who are the primary users? (sales staff, administrators, etc.)
3. Do you need search or filtering functionality?"
```

### Step 2: Clarification
Always confirm unclear points. Specifically verify:
- Concrete input/output formats
- External APIs or data sources
- Error handling requirements
- Priority of required vs. optional features

### Step 3: Present Initial Draft
Create and present the first draft based on gathered information.

### Step 4: Iterative Improvement
Upon receiving feedback:
1. Explain changes clearly
2. Show why changes are effective
3. Present updated spec
4. Request approval again

## Thinking Process

Execute these steps explicitly for each specification:

### 1. Requirements Analysis
Identify the core functionality, purpose, and target users from user requests.

### 2. Functional Decomposition
Break down requirements into implementable units. Each function should:
- Be valuable from the user's perspective
- Have a unique ID (FUNC-XXX)
- Have clear dependencies
- Have explicit priority (必須/推奨/オプション)

### 3. UI/UX Design
Design basic user interface and experience for each function, including:
- Screen layouts
- Key components
- User operation flows

### 4. Technical Specification Definition
Define component specifications (properties, state management, event handling) as clear instructions for the app builder, not as code.

### 5. Spec Structuring
Organize all information according to the output format below.

## Output Format

All specifications must use this markdown structure:

```markdown
## 1. アプリケーション概要
- **アプリケーション名**: [Clear, specific name]
- **目的**: [Problem this app solves or goal it achieves]
- **対象ユーザー**: [User demographics, characteristics, usage scenarios]

## 2. 機能要件

### 機能一覧
- **FUNC-001**: [Concise function description]
  - 依存: なし/FUNC-XXX
  - 優先度: 必須/推奨/オプション
- **FUNC-002**: [Concise function description]
  - 依存: FUNC-001
  - 優先度: 必須/推奨/オプション

### 各機能の詳細要件

#### FUNC-001: [Function name]

**ユーザーストーリー**:  
As a [role], I want [feature], so that [benefit]

**受け入れ基準** (System behavior clearly described):
1. WHEN [user action/event] THEN [system response/behavior]
2. IF [precondition] THEN [system response/behavior]
3. WHILE [continuation condition] [state system maintains]

**エッジケース・エラーハンドリング**:
- [Expected edge cases and handling]

**実装の優先順位**: 必須/推奨/オプション

#### FUNC-002: [Function name]
[Same format]

## 3. 画面設計とUIコンポーネント

### [Screen/Component name]
**概要**: [Role of this screen/component and when it displays]

**配置コンポーネント**:

#### [Component type]: [Component name]
- **目的**: [What users do with this component]
- **表示内容**: [What it displays]
- **動作**: [How it responds to user actions]
- **状態管理**: [States or variables to maintain]
- **関連機能**: FUNC-XXX

#### [Next component]
[Same format]

**ユーザーフロー**:
1. [User's first action]
2. [System response]
3. [Next action...]

## 4. 技術的補足
- **状態管理の方針**: [Global state/local state distinction]
- **外部連携**: [API usage, authentication methods, etc.]
- **パフォーマンス考慮点**: [Large data processing, real-time updates, etc.]
```

## File Creation

If `Spec.md` doesn't exist, always create it using the template in `assets/spec_template.md`. Copy the template and fill it with the structured specification.

## Quality Checklist

Before presenting a spec, verify:

- [ ] No code included (HTML, CSS, TypeScript, JSX)
- [ ] Each function has user story and acceptance criteria
- [ ] Dependencies and priorities clearly stated
- [ ] Error cases and edge cases addressed
- [ ] User flow is clear
- [ ] Within React + TypeScript + index.html/tsx constraints
- [ ] No ambiguous expressions
- [ ] App builder can interpret and implement

## Best Practices

### Writing Style
- Use imperative/infinitive form (verb-first instructions)
- Write "When user clicks button, system processes text" not "<button onClick={handler}>"
- Focus on behavior and outcomes, not implementation details
- Be specific about user actions and system responses

### Common Pitfalls to Avoid
- Including actual code snippets
- Being too vague about system behavior
- Forgetting error handling
- Not considering edge cases
- Omitting state management needs
- Unclear component purposes

### Good Specification Characteristics
- Describes "what" happens, not "how" it's implemented
- Clear acceptance criteria using WHEN/IF/WHILE patterns
- Explicit error handling for edge cases
- User-centric language throughout
- Traceability between features and UI components

## Using References

For detailed examples of good vs. bad specifications, refer to `references/examples.md`. This file contains:
- Common mistakes in specification writing
- High-quality specification examples
- UI component description patterns
- Acceptance criteria examples

Load this reference file when:
- User needs clarification on what makes a good spec
- Showing examples would be helpful
- Quality issues appear in draft specs

## Your Role

Act as a translator between users and the app builder. Your mission:
1. Understand user intent accurately
2. Transform it into app builder-interpretable format
3. Ensure specifications are complete and unambiguous
4. Guide users through iterative refinement
5. Always request explicit approval before completion

Remember: You create instructions for the builder, not code for the application.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kongyo2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

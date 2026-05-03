---
name: self-review
description: タスク完了前のセルフレビュー。Gemini CLI + Claude subagent によるマルチ視点検証。 Use when this capability is needed.
metadata:
  author: k9i-0
---

# Self Review Skill

タスクを完了（TodoWrite completed）とマークする前にこのセルフレビューを実行。

## Trigger Conditions

- TodoWrite でタスクを `completed` にマークする直前
- ユーザーが `/self-review` コマンドを明示的に実行した時

## Review Procedure

### Phase 1: Collect Change Diffs

```bash
# 変更ファイル一覧
git diff --name-only HEAD

# 変更内容取得
git diff HEAD
```

### Phase 2: Gemini CLI Review (External Model Perspective)

```bash
git diff HEAD -- "*.ts" "*.tsx" | gemini -p "
Please review the following code changes.

## Review Points (Check All)

### 1. Code Quality
- Readability (is it easy to understand?)
- Naming (appropriate variable/function names)
- Structure (proper separation of concerns)

### 2. Bugs & Errors
- TypeScript type safety
- Null/undefined handling
- React hooks rules
- Memory leaks (useEffect cleanup)
- Edge cases

### 3. Design Patterns
- Architecture consistency
- Zustand store patterns
- Repository pattern usage
- Component composition

### 4. React Native Specific
- Performance (FlatList optimization, memo usage)
- Platform differences (iOS/Android)
- Accessibility

## Output Format
- Critical issues: [file:line] [description and fix suggestion]
- Minor issues: [file:line] [description]
- No issues: 'LGTM'
"
```

### Phase 3: Claude Subagent Review

Task ツールで general-purpose エージェントを起動:

```
subagent_type: general-purpose

Prompt:
---
Please review the following code changes.

## Changed Files
[git diff --name-only HEAD result]

## Change Contents
[git diff HEAD result]

## Review Points

### Common Points
1. Code quality
2. Bugs & errors (TypeScript, hooks, edge cases)
3. Design patterns

### Project-Specific Points
4. Naming (files: camelCase/PascalCase, variables: camelCase, types: PascalCase)
5. Architecture (stores/, repositories/, screens/, components/)
6. React Native specific (performance, accessibility)
7. Zustand patterns (create, get, set usage)

## Output Format
- Critical issues: [file:line] [description and fix suggestion]
- Minor issues: [file:line] [description]
- No issues: 'LGTM'
---
```

### Phase 4: Result Integration

| 判定 | 条件 | アクション |
|------|------|----------|
| PASS | 両方 LGTM | タスク完了可能 |
| MINOR | 軽微な問題のみ | 警告表示、タスク完了可能 |
| FAIL | 重大な問題あり | 修正タスク追加、再レビュー必要 |

### Phase 5: Feedback Loop

FAIL 判定時:
1. 問題箇所を修正
2. Phase 1-4 を再実行
3. PASS になるまで繰り返す

## Review Checklist

### Code Quality
- [ ] 関数は単一責任原則に従う
- [ ] 適切なエラーハンドリング
- [ ] コメントは最小限で明確
- [ ] マジックナンバーは定数定義

### TypeScript
- [ ] any 型の使用を避ける
- [ ] 適切な型定義
- [ ] null/undefined の安全な処理

### React / React Native
- [ ] Hooks rules に従う
- [ ] useEffect のクリーンアップ
- [ ] 適切なメモ化（useMemo, useCallback, memo）
- [ ] FlatList の keyExtractor

### Zustand
- [ ] Store 構造が適切
- [ ] 非同期アクションのエラーハンドリング
- [ ] Optimistic update の適切な使用

## Output Template

```markdown
## Self Review Result

### Gemini Review (External Model)
[Output from Gemini]

### Claude Subagent Review (Different Context)
[Output from subagent]

### Comparison Analysis
- Common issues: [Issues identified by both]
- Gemini-only issues: [...]
- Subagent-only issues: [...]

### Judgment: [PASS/MINOR/FAIL]

#### Issues (if applicable)
- [ ] [file:line] [description]

#### Next Action
- [Complete task / Fix required]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/k9i-0) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

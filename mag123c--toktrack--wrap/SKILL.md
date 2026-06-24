---
name: wrap
description: Session end - document updates, commit Use when this capability is needed.
metadata:
  author: mag123c
---

# Wrap

## Flow
```
Git Status → Doc Check → User Selection → Execute → Complete
```

## Execution

1. **Git Status**
   ```bash
   git status --short
   git diff --stat HEAD~3
   ```

2. **Doc Check Checklist (MUST)**
   | Change | Target | Required |
   |--------|--------|----------|
   | trait/type | architecture.md | 구조 변경 시 |
   | convention | conventions.md | 새 패턴 시 |
   | **결정사항** | **.dev/DECISIONS.md** | **새 기능/설계 시** |

   **DECISIONS.md 기록 (필수 체크)**:
   - [ ] 새 기능 구현 → 결정 배경, 대안, 이유 기록
   - [ ] PLAN과 실제 구현 차이 → `**구현 노트**` 추가
   - [ ] 새로운 패턴/컨벤션 → conventions.md도 업데이트

3. **User Selection**: AskUserQuestion
4. **Execute**: Run selected items

## DSL Rules (ai-context)
- Table > prose
- Codeblock > description
- Core only, minimize lines

## Commit
```
{type}({scope}): {summary}
```

## Completion
wrap complete = **skill chain finished**
Next task starts with new `/clarify` or `/next`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mag123c) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

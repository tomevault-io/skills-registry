---
name: end-web
description: Web 프로젝트 작업 세션을 종료하고 요약을 생성합니다. 변경사항, 완료된 작업, 다음 할 일을 기록합니다. Use when this capability is needed.
metadata:
  author: choihyunjin
---

# Web 세션 종료

Web 프로젝트 작업 세션을 종료할 때 사용하는 스킬입니다.

## 사용 시점

- 작업 세션을 마무리할 때
- 오늘 작업 내용을 기록하고 싶을 때
- 다음 작업자(또는 미래의 나)를 위해 컨텍스트를 남기고 싶을 때

## 수행 작업

1. **변경사항 요약**
   - 수정/추가/삭제된 파일 목록
   - 주요 변경 내용 요약

2. **완료된 작업 정리**
   - 이번 세션에서 완료한 작업
   - 해결한 이슈/버그

3. **미완료 작업 기록**
   - 진행 중인 작업
   - 다음에 해야 할 일
   - 블로커나 주의사항

4. **세션 로그 저장**
   - `.claude/sessions/YYYY-MM-DD-HH-MM.md` 형식으로 저장
   - 다음 세션에서 참조 가능

## 호출 방법

```
/end-web
```

## Instructions for Claude

When this skill is invoked:

1. Run `git status` to check uncommitted changes
2. Run `git diff --stat` to see what files changed
3. Review the conversation to summarize completed work
4. Identify any incomplete tasks or blockers
5. Generate a session summary in markdown format
6. Save the summary to `.claude/sessions/YYYY-MM-DD-HH-MM.md`
7. Ask if user wants to commit changes before ending

## Session Summary Template

```markdown
# Web 세션 요약 - {날짜}

## 작업 브랜치
- {브랜치명}

## 완료된 작업
- [ ] 작업 1
- [ ] 작업 2

## 변경된 파일
- `path/to/file1.ts` - 설명
- `path/to/file2.tsx` - 설명

## 미완료/다음 작업
- [ ] 다음에 할 일 1
- [ ] 다음에 할 일 2

## 메모
- 특이사항이나 주의점
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/choihyunjin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

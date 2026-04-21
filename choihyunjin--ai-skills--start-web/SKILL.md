---
name: start-web
description: Web 프로젝트 작업 세션을 시작합니다. Git 상태, 최근 커밋, 변경사항을 확인하고 작업 컨텍스트를 로드합니다. Use when this capability is needed.
metadata:
  author: choihyunjin
---

# Web 세션 시작

Web 프로젝트 작업 세션을 시작할 때 사용하는 스킬입니다.

## 사용 시점

- 새로운 작업 세션을 시작할 때
- 이전 작업 컨텍스트를 확인하고 싶을 때
- 현재 프로젝트 상태를 파악하고 싶을 때

## 수행 작업

1. **Git 상태 확인**
   - 현재 브랜치
   - 변경된 파일 (staged/unstaged)
   - 추적되지 않는 파일

2. **최근 커밋 히스토리**
   - 최근 5개 커밋 표시
   - 커밋 메시지와 작성자

3. **이전 세션 요약 확인**
   - `.claude/sessions/` 폴더의 최근 세션 로그 확인
   - 이전에 진행하던 작업 컨텍스트 로드

4. **작업 준비**
   - 오늘 할 작업 확인/계획
   - TodoWrite로 작업 목록 초기화

## 호출 방법

```
/start-web
```

## Instructions for Claude

When this skill is invoked:

1. Run `git status` to check current branch and changes
2. Run `git log --oneline -5` to show recent commits
3. Check `.claude/sessions/` for the most recent session summary
4. If previous session exists, summarize what was done
5. Ask the user what they want to work on today
6. Create a todo list with TodoWrite if tasks are identified

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/choihyunjin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

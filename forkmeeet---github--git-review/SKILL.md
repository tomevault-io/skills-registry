---
name: git-review
description: 커밋 전에 git diff를 리뷰하고 문제를 한국어로 보고하는 스킬 Use when this capability is needed.
metadata:
  author: forkmeeet
---

# Git Review Skill (한국어 출력 필수)

⚠️ **모든 응답은 반드시 한국어로 작성한다.**
⚠️ 영어로 응답하지 않는다.
⚠️ 코드, 파일명, 키워드를 제외한 설명은 전부 한국어로 작성한다.

## 목적

커밋 전에 변경된 코드를 분석하여
- 버그 가능성
- 설계 문제
- 중복 코드
- 성능/보안 이슈

를 **한국어로 명확하게 피드백한다.**

## 출력 형식 (반드시 이 형식을 지킨다)

출력 규칙 (중요):

- 마크다운 문법을 절대 사용하지 않는다.
- '**', '#', '##', '-', '*', '>' 같은 마크다운 기호를 사용하지 않는다.
- 제목, 섹션명, 강조 표현에도 '**'를 사용하지 않는다.
- 모든 출력은 순수 평문(plain text)으로 작성한다.
- 첫 줄에는 반드시 PASS, WARN, FAIL 중 하나만 출력한다.
- 첫 줄에는 다른 문자, 설명, 장식, 공백을 절대 포함하지 않는다.
- 두 번째 줄부터 사람이 읽는 설명을 작성한다.


```plaintext
코드 리뷰 요약

변경 파일: X개
추가: Y줄 / 삭제: Z줄

[잘된 점]
- …

[발견된 문제]

(치명적)
- filename.ext:line
  문제:
  이유:
  해결 방안:

(중요)
- …

(경미 / 스타일)
- …

[개선 제안]
- …

[최종 판단]
PASS  커밋 가능
WARN  수정 권장
FAIL  커밋 금지
```

## 범위

- git diff에 포함된 코드만 리뷰한다.
- 사용자가 명시적으로 요청하지 않은 규칙이나 베스트 프랙티스는 강제로 적용하지 않는다.
- 변경된 코드의 맥락이 부족하면 "알 수 없음"이라고 명확히 작성한다.
- 변경된 코드가 너무 적거나 없으면 "리뷰할 변경 사항이 없습니다."라고 작성한다.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/forkmeeet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

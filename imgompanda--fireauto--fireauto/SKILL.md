---
name: fireauto-knowledge-hierarchy-guide
description: > Use when this capability is needed.
metadata:
  author: imgompanda
---

# 지식 계층 탐색 가이드

## 탐색 순서

1. **CLAUDE.md** 확인 (이미 로드됨) — 핵심 규칙, 주의사항, Wiki/스킬 포인터
2. **wiki-read** 호출 — patterns, gotchas, decisions 등 상세 페이지
3. **skill-search** 호출 — DB에서 관련 스킬 검색
4. **memory-search** 호출 — 과거 작업 기록에서 관련 맥락

---

## 언제 뭘 쓰나

| 상황 | 탐색 대상 |
|------|-----------|
| 코딩 패턴 | `wiki-read patterns` |
| 주의사항 | `wiki-read gotchas` |
| 설계 이유 | `wiki-read decisions` |
| 재사용 코드 | `skill-search` |
| 과거 작업 | `memory-search` |

---

## 톤 & 스타일

- **문체**: 토스체 — 친근하고 간결하게
- **분량**: 핵심만 짧게. 장황하게 설명하지 않아요
- **행동 유도**: 검색 결과를 답변에 바로 반영해요

---
> Source: [imgompanda/fireauto](https://github.com/imgompanda/fireauto) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-27 -->

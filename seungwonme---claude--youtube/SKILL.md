---
name: youtube
description: 유튜브 채널 운영 전문가. 콘텐츠 기획부터 제목/썸네일, 영상 제작, 채널 관리, 수익화, 영상 요약까지 전 영역 지원. Routes to the appropriate sub-skill. Trigger on "유튜브", "YouTube", "영상 기획", "콘텐츠 기획", "아웃라이어", "알고리즘", "썸네일", "제목 공식", "클릭률", "영상 제작", "원고", "인트로", "편집", "채널 브랜딩", "댓글 관리", "수익화", "키 콘텐츠", "풀링 콘텐츠", "객단가", "유튜브 정리", "영상 요약", "transcript 번역", "YouTube digest", "영상 퀴즈". Sub-skills — 기획 (콘텐츠 기획, 알고리즘 전략, 아웃라이어 제작, 시청자 풀 분석), 썸네일 (제목/썸네일 제작, 19가지 제목 공식, A/B 테스트), 제작 (원고 작성, 인트로 구성, 편집 기법, 시청자 참여 유도), 채널 (채널 브랜딩, 이미지 관리, 댓글 관리), 수익화 (키/풀링 콘텐츠, 상품 판매 전략, 객단가 최적화), 요약 (YouTube 영상 분석, transcript 추출/번역, 퀴즈, Deep Research). Use when this capability is needed.
metadata:
  author: seungwonme
---

# YouTube: 유튜브 채널 운영 라우터

사용자의 요청에 맞는 sub-skill로 라우팅.

## Sub-skill Selection

| Sub-skill | When | Read |
| --------- | ---- | ---- |
| **기획** | 콘텐츠 주제 기획, 알고리즘 전략, 아웃라이어 제작, 시청자 풀 분석 | `기획/SKILL.md` |
| **썸네일** | 제목/썸네일 제작, 19가지 제목 공식, 클릭률 최적화, A/B 테스트 | `썸네일/SKILL.md` |
| **제작** | 원고 작성, 인트로 구성, 편집 기법, 시청자 참여 유도 | `제작/SKILL.md` |
| **채널** | 채널 브랜딩, 이미지 형성, 댓글 관리, 커뮤니티 운영 | `채널/SKILL.md` |
| **수익화** | 키/풀링 콘텐츠 기획, 상품 판매 전략, 가격/객단가 최적화 | `수익화/SKILL.md` |
| **요약** | YouTube URL 분석, transcript 추출/번역, 요약/인사이트, 퀴즈 | `요약/SKILL.md` |

## Decision Logic

1. **YouTube URL 제공 또는 영상 요약/정리/퀴즈 요청** → `요약` (transcript 추출 + 분석 + 퀴즈)
2. **영상 주제·아이디어·알고리즘 관련** → `기획` (시청자 풀 + 아웃라이어 전략)
3. **제목·썸네일·표지 관련** → `썸네일` (19가지 공식 + A/B 테스트)
4. **원고·촬영·편집·인트로 관련** → `제작` (공감 3요소 + 정보 6요소 + 편집 기법)
5. **브랜딩·댓글·이미지·커뮤니티 관련** → `채널` (브랜딩 + 댓글 관리)
6. **돈·상품·수익·판매 관련** → `수익화` (키/풀링 콘텐츠 + 가격 전략)

If unclear which sub-skill fits, use AskUserQuestion:

```
questions:
  - question: "어떤 유튜브 운영 영역에 대해 도움이 필요한가요?"
    header: "유형"
    options:
      - label: "영상 요약/분석"
        description: "YouTube 영상 transcript 추출, 요약, 번역, 퀴즈"
      - label: "콘텐츠 기획"
        description: "영상 주제 선정, 알고리즘 전략, 아웃라이어 제작법"
      - label: "제목/썸네일/제작"
        description: "표지 제작, 원고 작성, 인트로, 편집"
      - label: "채널 관리/수익화"
        description: "채널 브랜딩, 댓글 관리, 수익화 전략, 상품 판매"
    multiSelect: false
```

After selection, read the corresponding sub-skill's SKILL.md and follow its protocol.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seungwonme) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

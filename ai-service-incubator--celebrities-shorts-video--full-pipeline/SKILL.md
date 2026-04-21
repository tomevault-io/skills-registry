---
name: full-pipeline
description: 연예인 화장품 숏폼 콘텐츠 전체 파이프라인. 연예인명만 입력하면 리서치부터 포스팅 최적화까지 모든 단계를 자동으로 실행합니다. (팀 에이전트 기반) Use when this capability is needed.
metadata:
  author: ai-service-incubator
---

# Full Pipeline Skill (Team-Based Architecture)

연예인 화장품 숏폼 콘텐츠 제작의 모든 단계를 **팀 에이전트 기반**으로 실행하는 통합 파이프라인입니다.

## 사용법

```
/full-pipeline [연예인명]
```

예시:
```
/full-pipeline 아이유
/full-pipeline 장원영
/full-pipeline 차은우
```

## 팀 기반 아키텍처

```
┌───────────────────────────────────────────────────────────────────────┐
│                        FULL PIPELINE (Team-Based)                      │
├───────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   ┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐  │
│   │  RESEARCH-TEAM  │ →  │ PRODUCTION-TEAM  │ →  │ PUBLISHING-TEAM │  │
│   │                 │    │                  │    │                 │  │
│   │ • sns-discoverer│    │ • script-writer  │    │ • trend-analyst │  │
│   │ • content-      │    │ • asset-searcher │    │ • caption-writer│  │
│   │   analyzer      │    │ • asset-         │    │ • scheduler     │  │
│   │ • fact-checker  │    │   downloader     │    │                 │  │
│   │                 │    │ • edit-director  │    │                 │  │
│   │                 │    │ • video-renderer │    │                 │  │
│   │                 │    │ • thumbnail-     │    │                 │  │
│   │                 │    │   designer       │    │                 │  │
│   └─────────────────┘    └──────────────────┘    └─────────────────┘  │
│           ↓                       ↓                       ↓            │
│    research_complete.json   video.mp4 + script.md   posting_guide.json│
│                                                                         │
└───────────────────────────────────────────────────────────────────────┘
```

## 실행 프로세스

### Team 1: Research Team

**Gate Agent가 순차 실행:**
```
Task: @research-team "[연예인명]에 대한 화장품 리서치 시작"
```

**내부 플로우:**
1. `sns-discoverer` → 공식 SNS 채널 탐색
2. `content-analyzer` → 콘텐츠 분석, 제품 정보 추출
3. `fact-checker` → 제품 정보 검증, 신뢰도 등급 부여

**출력:**
- `data/research/channels/[연예인명]_channels.json`
- `data/research/products/[연예인명]_products.json`
- `data/verified/[연예인명]_verified.json`
- `data/research/[연예인명]_research_complete.json`

---

### Team 2: Production Team

**Gate Agent가 순차 실행:**
```
Task: @production-team "리서치 결과 기반 콘텐츠 제작"
```

**내부 플로우:**
1. `script-writer` → 60초 숏폼 스크립트 작성
2. `asset-searcher` → 필요 이미지 URL 수집
3. `asset-downloader` → 이미지 다운로드
4. `edit-director` → 편집 지시서 작성
5. `video-renderer` → 영상 생성 (선택적)
6. `thumbnail-designer` → 썸네일 가이드 작성

**출력:**
- `scripts/[연예인명].md`
- `assets/images/celebrity/`, `assets/images/products/`
- `data/production/[연예인명]_edit_instructions.md`
- `output/[연예인명].mp4` (선택적)
- `data/thumbnails/[연예인명]_thumbnail_guide.md`

---

### Team 3: Publishing Team

**Gate Agent가 순차 실행:**
```
Task: @publishing-team "포스팅 최적화"
```

**내부 플로우:**
1. `trend-analyst` → 트렌드 분석, 해시태그 수집
2. `caption-writer` → 플랫폼별 캡션 작성
3. `scheduler` → 최적 포스팅 시간 계산

**출력:**
- `data/posting/[연예인명]_trends.json`
- `data/posting/[연예인명]_captions.json`
- `data/posting/[연예인명]_schedule.json`
- `data/posting/[연예인명]_posting_guide.json`

---

## 최종 산출물

```
project/
├── data/
│   ├── research/
│   │   ├── channels/
│   │   │   └── [연예인명]_channels.json
│   │   ├── products/
│   │   │   └── [연예인명]_products.json
│   │   └── [연예인명]_research_complete.json
│   ├── verified/
│   │   └── [연예인명]_verified.json
│   ├── assets/
│   │   ├── [연예인명]_asset_list.json
│   │   └── [연예인명]_download_report.json
│   ├── production/
│   │   ├── [연예인명]_edit_instructions.md
│   │   └── [연예인명]_video_script.py
│   ├── thumbnails/
│   │   └── [연예인명]_thumbnail_guide.md
│   ├── posting/
│   │   ├── [연예인명]_trends.json
│   │   ├── [연예인명]_captions.json
│   │   ├── [연예인명]_schedule.json
│   │   └── [연예인명]_posting_guide.json
│   └── context/
│       └── [session-id]/
│           └── handoff_*.json
├── scripts/
│   └── [연예인명].md
├── assets/
│   ├── images/
│   │   ├── celebrity/
│   │   └── products/
│   └── audio/
└── output/
    └── [연예인명].mp4
```

## 진행 상황 리포트

```
═══════════════════════════════════════════════════════════════════
   [연예인명] 콘텐츠 파이프라인 진행 현황 (Team-Based)
═══════════════════════════════════════════════════════════════════

[✓] RESEARCH-TEAM
    ├─ sns-discoverer: YouTube 1개, Instagram 1개 확인
    ├─ content-analyzer: 제품 15개 발견
    └─ fact-checker: A등급 3개, B등급 2개 검증

[✓] PRODUCTION-TEAM
    ├─ script-writer: 58초 스크립트 완성
    ├─ asset-searcher: 15개 이미지 URL 수집
    ├─ asset-downloader: 12개 다운로드 완료
    ├─ edit-director: 편집 지시서 완성
    ├─ video-renderer: MP4 생성 완료
    └─ thumbnail-designer: 썸네일 가이드 완성

[✓] PUBLISHING-TEAM
    ├─ trend-analyst: 45개 해시태그 수집
    ├─ caption-writer: 3개 플랫폼 캡션 완성
    └─ scheduler: 금요일 21:00 TikTok 시작 권장

═══════════════════════════════════════════════════════════════════
   최종 결과
═══════════════════════════════════════════════════════════════════
   📄 스크립트: scripts/[연예인명].md
   🎬 영상: output/[연예인명].mp4
   📱 포스팅 가이드: data/posting/[연예인명]_posting_guide.json

   추천 포스팅 일정:
   • TikTok: 금요일 21:00 KST
   • Instagram: 토요일 12:00 KST
   • YouTube: 일요일 16:00 KST
═══════════════════════════════════════════════════════════════════
```

## 에러 처리

### 팀별 폴백

| 팀 | 문제 상황 | 대응 |
|-----|----------|------|
| research-team | 채널/콘텐츠 없음 | 뷰티 매체/인터뷰로 대체 |
| production-team | 에셋 부족 | 편집 지시서만 제공 |
| publishing-team | 트렌드 API 제한 | 기본 해시태그 세트 |

### 중단 조건
- 검증된 제품이 1개 미만
- 연예인 공식 채널 없음
- 사용자 중단 요청

## 옵션

```
/full-pipeline [연예인명] --skip-video       # 영상 생성 스킵
/full-pipeline [연예인명] --quick            # 빠른 모드
/full-pipeline [연예인명] --team=research    # 특정 팀만 실행
/full-pipeline [연예인명] --platform=tiktok  # 특정 플랫폼만
```

### 특정 팀만 실행
```
/full-pipeline 장원영 --team=research
/full-pipeline 장원영 --team=production
/full-pipeline 장원영 --team=publishing
```

## 주의사항

1. **팀 순차 실행**: research → production → publishing 순서 유지
2. **컨텍스트 전달**: 각 팀의 결과가 다음 팀의 입력으로 전달됨
3. **부분 실행**: --team 옵션으로 특정 팀만 실행 가능
4. **에러 복구**: 팀 단위로 재실행 가능

## 실행 예시

### 전체 파이프라인
```
User: /full-pipeline 장원영

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ai-service-incubator) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

---
name: dev-doc-system
description: 개발 문서 통합 관리 시스템. 개발 방향, 진행 상황, 계획, 변경 사항, 피드백을 체계적으로 기록하고 관리. "문서 시스템", "개발 기록", "방향 설정", "회고", "변경 이력" 키워드에 반응. Use when this capability is needed.
metadata:
  author: tygwan
---

# Dev Doc System - 개발 문서 통합 관리 시스템

프로젝트의 개발 방향성, 진행 상황, 변경 사항, 피드백을 체계적으로 기록하고 일관성을 유지하는 통합 문서 관리 시스템입니다.

## 핵심 가치

- **일관성**: 개발 방향과 결정 사항을 기록하여 방향성 유지
- **추적성**: 모든 변경과 결정의 이유를 기록하여 맥락 보존
- **투명성**: 현재 상황과 계획을 명확히 공유
- **학습**: 문제와 피드백을 기록하여 반복 실수 방지

---

## 문서 구조

```
docs/
├── direction/           # 개발 방향 (왜, 무엇을)
│   ├── VISION.md        # 프로젝트 비전 및 목표
│   ├── ROADMAP.md       # 개발 로드맵
│   └── DECISIONS.md     # 주요 결정 사항 기록 (ADR)
│
├── status/              # 현재 상황 (지금 무엇을)
│   ├── CURRENT.md       # 현재 진행 중인 작업
│   └── CHANGELOG.md     # 변경 이력
│
├── planning/            # 앞으로의 계획 (다음에 무엇을)
│   ├── BACKLOG.md       # 백로그 및 우선순위
│   └── NEXT-SPRINT.md   # 다음 스프린트/마일스톤
│
├── changes/             # 중간 변경 사항 (무엇이 바뀌었나)
│   ├── SCOPE-CHANGES.md # 스코프 변경 기록
│   └── PIVOT-LOG.md     # 방향 전환 기록
│
├── feedback/            # 문제 및 피드백 (무엇을 배웠나)
│   ├── ISSUES.md        # 이슈 및 블로커 기록
│   ├── RETRO.md         # 스프린트/마일스톤 회고
│   └── LEARNINGS.md     # 교훈 및 베스트 프랙티스
│
├── prd/                 # PRD 문서 (기존)
├── tech-specs/          # 기술 설계서 (기존)
├── progress/            # 진행상황 체크 (기존)
│
└── README.md            # 문서 인덱스 + 프로젝트 상태 헤더
```

---

## 문서 유형별 가이드

### 1. Direction (개발 방향)

#### VISION.md - 프로젝트 비전
```markdown
# 프로젝트 비전

## 미션
{한 문장으로 프로젝트의 존재 이유}

## 핵심 가치
1. {가치 1}: {설명}
2. {가치 2}: {설명}
3. {가치 3}: {설명}

## 성공 지표 (KPI)
| 지표 | 목표 | 현재 | 상태 |
|------|------|------|------|
| {지표 1} | {목표값} | {현재값} | 🔄 |

## 비전 변경 이력
| 날짜 | 변경 내용 | 이유 |
|------|----------|------|
```

#### ROADMAP.md - 개발 로드맵
```markdown
# 개발 로드맵

## 현재 Phase: {Phase N} - {Phase 이름}

### Phase 1: {이름} ✅
- 기간: YYYY-MM-DD ~ YYYY-MM-DD
- 목표: {목표}
- 결과: {결과 요약}

### Phase 2: {이름} 🔄
- 기간: YYYY-MM-DD ~ YYYY-MM-DD
- 목표: {목표}
- 진행률: `████████░░░░░░░░░░░░` 40%

### Phase 3: {이름} ⏳
- 기간: YYYY-MM-DD ~ (예정)
- 목표: {목표}
- 의존성: Phase 2 완료 필요
```

#### DECISIONS.md - 아키텍처 결정 기록 (ADR)
```markdown
# 아키텍처 결정 기록 (ADR)

## ADR-001: {결정 제목}
- **날짜**: YYYY-MM-DD
- **상태**: 승인됨 | 대체됨 | 폐기됨
- **결정자**: {이름}

### 맥락
{이 결정이 필요한 배경}

### 결정
{내린 결정}

### 대안
1. {대안 1}: {장단점}
2. {대안 2}: {장단점}

### 결과
{예상되는 결과 및 영향}

### 변경 이력
| 날짜 | 변경 | 이유 |
|------|------|------|
```

---

### 2. Status (현재 상황)

#### CURRENT.md - 현재 진행 작업
```markdown
# 현재 진행 상황

**마지막 업데이트**: YYYY-MM-DD HH:MM

## 이번 주 목표
- [ ] {목표 1}
- [ ] {목표 2}

## 진행 중인 작업
| 작업 | 담당 | 시작일 | 상태 | 블로커 |
|------|------|--------|------|--------|
| {작업} | {담당} | {날짜} | 🔄 | {없음/있음} |

## 완료된 작업 (이번 주)
| 작업 | 담당 | 완료일 |
|------|------|--------|
| {작업} | {담당} | {날짜} |

## 블로커/이슈
| 이슈 | 영향 | 해결책 | 상태 |
|------|------|--------|------|
| {이슈} | {영향} | {해결책} | 🔴 |
```

#### CHANGELOG.md - 변경 이력
```markdown
# 변경 이력

모든 주요 변경 사항을 기록합니다.

## [Unreleased]

### Added
- {새로 추가된 기능}

### Changed
- {변경된 기능}

### Fixed
- {수정된 버그}

### Removed
- {제거된 기능}

## [1.0.0] - YYYY-MM-DD
...
```

---

### 3. Planning (앞으로의 계획)

#### BACKLOG.md - 백로그
```markdown
# 백로그

## 우선순위 정의
- **P0**: 크리티컬 - 즉시 처리
- **P1**: 높음 - 이번 스프린트
- **P2**: 중간 - 다음 스프린트
- **P3**: 낮음 - 백로그

## 백로그 항목

### P0 - 크리티컬
| ID | 항목 | 예상 공수 | 의존성 |
|----|------|----------|--------|
| BL-001 | {항목} | {공수} | {의존성} |

### P1 - 높음
...

### P2 - 중간
...

### P3 - 낮음
...

### Ice Box (보류)
| ID | 항목 | 보류 이유 |
|----|------|----------|
```

#### NEXT-SPRINT.md - 다음 스프린트
```markdown
# 다음 스프린트 계획

## 스프린트 정보
- **스프린트**: #{번호}
- **기간**: YYYY-MM-DD ~ YYYY-MM-DD
- **목표**: {스프린트 목표}

## 스프린트 범위
| 항목 | 우선순위 | 예상 공수 | 담당 |
|------|----------|----------|------|
| {항목} | P1 | {공수} | {담당} |

## 리스크
| 리스크 | 확률 | 영향 | 대응책 |
|--------|------|------|--------|

## 성공 기준
- [ ] {기준 1}
- [ ] {기준 2}
```

---

### 4. Changes (중간 변경 사항)

#### SCOPE-CHANGES.md - 스코프 변경
```markdown
# 스코프 변경 기록

## SC-001: {변경 제목}
- **날짜**: YYYY-MM-DD
- **요청자**: {이름}
- **유형**: 추가 | 제거 | 수정

### 원래 스코프
{변경 전 내용}

### 변경된 스코프
{변경 후 내용}

### 변경 이유
{왜 변경이 필요한가}

### 영향 분석
- **일정**: {영향}
- **비용**: {영향}
- **품질**: {영향}
- **리스크**: {영향}

### 승인
- [ ] PM 승인
- [ ] 기술 리드 승인
- [ ] 이해관계자 승인
```

#### PIVOT-LOG.md - 방향 전환 기록
```markdown
# 방향 전환 기록

## PV-001: {전환 제목}
- **날짜**: YYYY-MM-DD
- **전환 유형**: 기술 | 제품 | 비즈니스

### 이전 방향
{이전 접근 방식}

### 새 방향
{새로운 접근 방식}

### 전환 이유
{데이터, 피드백, 학습 등}

### 폐기되는 것
- {폐기 항목 1}
- {폐기 항목 2}

### 새로 필요한 것
- {신규 항목 1}
- {신규 항목 2}

### 교훈
{이 전환에서 배운 것}
```

---

### 5. Feedback (문제 및 피드백)

#### ISSUES.md - 이슈 기록
```markdown
# 이슈 기록

## 활성 이슈

### ISS-001: {이슈 제목} 🔴
- **발견일**: YYYY-MM-DD
- **심각도**: 크리티컬 | 높음 | 중간 | 낮음
- **영향**: {영향 범위}

#### 증상
{어떻게 발견되었나}

#### 근본 원인
{왜 발생했나}

#### 해결 방안
1. {방안 1}
2. {방안 2}

#### 진행 상황
- [ ] 원인 분석
- [ ] 해결책 구현
- [ ] 테스트
- [ ] 배포

---

## 해결된 이슈

### ISS-000: {이슈 제목} ✅
- **발견일**: YYYY-MM-DD
- **해결일**: YYYY-MM-DD
- **해결 방법**: {요약}
- **교훈**: {배운 점}
```

#### RETRO.md - 회고
```markdown
# 스프린트/마일스톤 회고

## 회고: {Phase/Sprint} #{번호}
- **날짜**: YYYY-MM-DD
- **참여자**: {참여자 목록}

### 잘한 것 (Keep)
- {항목 1}
- {항목 2}

### 개선할 것 (Problem)
- {항목 1}
- {항목 2}

### 시도할 것 (Try)
- {항목 1}
- {항목 2}

### 액션 아이템
| 액션 | 담당 | 기한 | 상태 |
|------|------|------|------|
| {액션} | {담당} | {기한} | ⏳ |

### 핵심 교훈
> {이번 회고의 가장 중요한 교훈 한 줄}
```

#### LEARNINGS.md - 교훈 및 베스트 프랙티스
```markdown
# 교훈 및 베스트 프랙티스

## 기술적 교훈

### LRN-001: {교훈 제목}
- **날짜**: YYYY-MM-DD
- **카테고리**: 아키텍처 | 코드 | 테스트 | 배포 | 기타

#### 상황
{어떤 상황에서 배웠나}

#### 교훈
{무엇을 배웠나}

#### 적용 방법
{앞으로 어떻게 적용할 것인가}

---

## 프로세스 교훈

### LRN-002: {교훈 제목}
...

---

## 커뮤니케이션 교훈

### LRN-003: {교훈 제목}
...
```

---

## 도구 조합 (Tool Combinations)

### 워크플로우별 도구 매핑

| 워크플로우 | 문서 | 도구 조합 |
|-----------|------|----------|
| 프로젝트 시작 | VISION.md, ROADMAP.md | `prd-writer` + `dev-doc-system` |
| 기능 기획 | PRD, BACKLOG.md | `prd-writer` |
| 기술 설계 | DECISIONS.md, tech-spec | `tech-spec-writer` |
| 개발 진행 | CURRENT.md, progress | `progress-tracker` |
| 스코프 변경 | SCOPE-CHANGES.md | `dev-doc-system` |
| 이슈 발생 | ISSUES.md | `dev-doc-system` + `git-troubleshooter` |
| 스프린트 완료 | RETRO.md, CHANGELOG.md | `dev-doc-system` + `commit-helper` |
| 문서 검증 | 전체 | `doc-validator` |
| README 업데이트 | README.md | `skill-manager` (update-header) |

### Hook 통합

```yaml
# .claude/hooks/doc-update.sh
# 커밋 후 자동 문서 업데이트 트리거

on_commit:
  - update: status/CURRENT.md
  - update: status/CHANGELOG.md

on_milestone:
  - create: feedback/RETRO.md
  - update: direction/ROADMAP.md

on_scope_change:
  - create: changes/SCOPE-CHANGES.md entry
  - update: planning/BACKLOG.md
```

---

## 명령어

### 초기화
```bash
# 전체 문서 구조 초기화
/dev-doc-system init

# 특정 카테고리만 초기화
/dev-doc-system init --category direction
```

### 문서 생성/업데이트
```bash
# 비전 문서 작성
/dev-doc-system vision "프로젝트 설명"

# 로드맵 업데이트
/dev-doc-system roadmap --phase 2 --status 40%

# 결정 기록 추가
/dev-doc-system decision "결정 제목"

# 현재 상황 업데이트
/dev-doc-system current

# 스코프 변경 기록
/dev-doc-system scope-change "변경 제목"

# 회고 생성
/dev-doc-system retro --sprint 3

# 교훈 기록
/dev-doc-system learning "교훈 제목"
```

### 문서 조회
```bash
# 문서 상태 요약
/dev-doc-system status

# 특정 문서 조회
/dev-doc-system show direction/ROADMAP.md
```

---

## 자동화 워크플로우

### 1. 일일 업데이트
```
매일 아침:
1. status/CURRENT.md 자동 갱신 (git log 기반)
2. feedback/ISSUES.md 활성 이슈 체크
3. planning/NEXT-SPRINT.md 진행률 업데이트
```

### 2. 주간 정리
```
매주 금요일:
1. status/CHANGELOG.md 주간 변경사항 정리
2. direction/ROADMAP.md 진행률 업데이트
3. planning/BACKLOG.md 우선순위 재검토
```

### 3. 마일스톤 완료
```
마일스톤 완료 시:
1. feedback/RETRO.md 회고 문서 생성
2. feedback/LEARNINGS.md 교훈 추가
3. direction/ROADMAP.md Phase 완료 표시
4. docs/README.md 헤더 업데이트
```

---

## Best Practices

### DO
- ✅ 결정할 때마다 DECISIONS.md에 기록
- ✅ 스코프 변경 시 SCOPE-CHANGES.md에 기록
- ✅ 문제 해결 후 LEARNINGS.md에 교훈 기록
- ✅ 마일스톤마다 RETRO.md 작성
- ✅ 문서 간 상호 참조 링크 유지

### DON'T
- ❌ 결정 이유 없이 변경
- ❌ 스코프 변경을 기록 없이 진행
- ❌ 같은 실수 반복 (LEARNINGS.md 참조)
- ❌ 회고 없이 다음 단계 진행
- ❌ 문서 업데이트 미루기

---

## 관련 도구

| 도구 | 유형 | 용도 |
|------|------|------|
| `prd-writer` | Agent | PRD 작성 |
| `tech-spec-writer` | Agent | 기술 설계서 작성 |
| `progress-tracker` | Agent | 진행상황 추적 |
| `doc-validator` | Agent | 문서 완성도 검증 |
| `commit-helper` | Agent | 커밋 메시지 및 CHANGELOG |
| `skill-manager` | Skill | README 헤더 업데이트 |
| `dev-doc-planner` | Command | 문서 플래닝 |
| `git-workflow` | Command | Git 워크플로우 |

---

## 참조 문서

- `references/document-templates.md` - 모든 템플릿
- `references/workflow-diagrams.md` - 워크플로우 다이어그램
- `references/integration-guide.md` - 도구 통합 가이드

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tygwan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

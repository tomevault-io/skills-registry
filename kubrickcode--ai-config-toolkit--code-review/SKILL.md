---
name: code-review
description: 현재 git 변경사항 또는 최신 커밋을 code-reviewer와 architect-reviewer 에이전트로 리뷰 Use when this capability is needed.
metadata:
  author: kubrickcode
---

# 코드 리뷰 커맨드

## 목적

현재 변경사항 또는 최신 커밋에 대한 종합적인 코드 리뷰 수행. **모든 출력은 한국어로 작성합니다.** 자동으로 호출되는 에이전트:

- **code-reviewer 에이전트**: 항상 (코드 품질, 보안, 유지보수성)
- **architect-reviewer 에이전트**: 조건부 (아키텍처 영향 평가)

---

## 워크플로우

### 1. 리뷰 대상 결정

**커밋되지 않은 변경사항 확인:**

```bash
git status --porcelain
```

**결정:**

- 변경사항 있음: `git diff HEAD`로 미커밋 변경사항 리뷰 (staged + unstaged)
- 변경사항 없음: `git log -1` 및 `git show HEAD`로 최신 커밋 리뷰
- 사용자가 특정 커밋 해시 지정 시: `git show <해시>`로 해당 커밋 리뷰

**빈 diff 가드:** diff 출력이 없으면 "리뷰할 변경사항이 없습니다"를 안내하고 중단.

### 2. 제외 패턴

diff 분석 및 에이전트 프롬프트에서 다음을 필터링:

- Lock 파일: `package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`, `go.sum`, `Cargo.lock`
- 생성/빌드: `dist/`, `build/`, `*.generated.*`, `*.min.*`
- 소스맵: `*.map`
- 바이너리/미디어: 이미지, 폰트, 컴파일 산출물

`git diff HEAD -- . ':!package-lock.json' ':!pnpm-lock.yaml' ':!yarn.lock' ':!go.sum' ':!Cargo.lock' ':!dist' ':!build'`로 제외 적용.

### 3. 변경 범위 분석

**git 출력에서 메트릭 추출:**

수치화:

- 변경된 파일 수
- 추가/삭제된 라인 수
- 영향받는 디렉토리 수
- 신규 파일 vs 수정 파일

탐지:

- 데이터베이스 마이그레이션 파일 (`migrations/`, `schema.prisma`, `*.sql`)
- API 계약 파일 (`*.graphql`, `openapi.yaml`, `*.proto`, API 라우트 파일)
- 설정 파일 (`package.json`, `go.mod`, `pyproject.toml`, `Cargo.toml`, `build.gradle`, `docker-compose.yml`)
- 인프라 파일 (`*.tf`, `Dockerfile`, K8s 매니페스트, CI/CD 설정)
- 보안 민감 파일 (`auth/`, `**/middleware/auth*`, `*.pem`, `*.key`)
- 신규 디렉토리 또는 모듈

### 4. code-reviewer 에이전트 호출

**항상 Task 도구로 subagent_type: "code-reviewer" 사용하여 실행:**

중복 탐색을 방지하기 위해 구조화된 컨텍스트 전달:

```
## 리뷰 컨텍스트

- **대상**: {미커밋 변경사항 / 커밋 <해시>}
- **작업 유형**: {feature/bugfix/refactor/chore/prototype}
- **커밋 메시지**: {git log 또는 commit_message.md의 메시지}

## 스코프 경계

아키텍처 관련 사항(컴포넌트 경계, 모듈 결합도, 시스템 확장성, 배포 아키텍처)은 리뷰 범위 밖입니다. 리뷰하지 마세요.

## Diff

{아래 diff 크기 규칙 참조}
```

**Diff 크기 규칙:**

- **≤ 500줄**: 전체 diff 인라인 포함
- **> 500줄**: `git diff --stat` 요약만 포함하고 지시: "상세 리뷰가 필요한 파일은 Read 도구로 선택적으로 읽으세요."

### 5. 조건부 architect-reviewer 호출

**범위 분류 기준** (해당 기준마다 점수 부여):

| 기준                | 임계값                                             | 점수 |
| ------------------- | -------------------------------------------------- | ---- |
| 변경된 파일 수      | ≥ 8개 파일                                         | +1   |
| 변경된 라인 수      | ≥ 300줄                                            | +1   |
| 영향받는 디렉토리   | ≥ 3개 디렉토리                                     | +1   |
| 새 모듈/패키지      | 3개 이상 파일이 있는 새 디렉토리                   | +1   |
| API 계약 변경       | 수정: schema/, .graphql, .proto, api/, 라우트 파일 | +2   |
| 데이터베이스 스키마 | 수정: migrations, 스키마 파일, ORM 모델            | +2   |
| 설정/인프라         | 수정: docker-compose, Dockerfile, K8s, CI/CD, .tf  | +1   |
| 의존성 변경         | 수정: package.json, go.mod, requirements.txt       | +1   |
| 보안 민감 파일      | 수정: auth/, middleware/auth*, *.pem, \*.key       | +2   |

**결정**:

- **점수 < 3** → `code-reviewer`만 호출
- **점수 ≥ 3** → `code-reviewer`와 `architect-reviewer` 모두 **병렬 호출** (하나의 메시지에 두 Task 호출)

**architect-reviewer 구조화된 컨텍스트:**

```
## 리뷰 컨텍스트

- **대상**: {미커밋 변경사항 / 커밋 <해시>}
- **작업 유형**: {feature/bugfix/refactor/chore/prototype}
- **커밋 메시지**: {메시지}
- **스코프 점수**: {점수} (트리거: {충족된 기준 목록})
- **핵심 변경 영역**: {영향이 큰 디렉토리/모듈}

## 스코프 경계

라인 수준 코드 품질(네이밍, 포맷팅, 개별 함수 로직, 테스트 커버리지)은 리뷰 범위 밖입니다. 리뷰하지 마세요.

## Diff

{code-reviewer와 동일한 diff 크기 규칙 적용}
```

---

## 오류 처리

- **빈 diff** → "리뷰할 변경사항이 없습니다" 안내 후 중단
- **Git 명령 실패** → 실패한 명령과 함께 에러 리포트
- **에이전트 실패** → 성공한 에이전트의 부분 결과 포함 + 실패한 에이전트 안내
- **에이전트가 이슈 없음 반환** → 해당 섹션에 "이슈 없음" 포함

---

## 출력 형식

**통합 리뷰 요약 생성:**

```markdown
# 코드 리뷰 보고서

**생성일**: {타임스탬프}
**범위**: {미커밋 변경사항 / 최신 커밋: {해시}}
**변경된 파일 수**: {개수}
**호출된 리뷰어**: code-reviewer{, 해당 시 architect-reviewer}

---

## 📊 변경사항 요약

- **라인**: +{추가} -{삭제}
- **파일**: {개수} ({신규} 신규, {수정} 수정)
- **디렉토리**: {영향받는 디렉토리}

---

## 🔍 코드 리뷰어 피드백

{code-reviewer 에이전트의 통합 출력}

---

## 🏗️ 아키텍처 리뷰어 피드백

{호출된 경우, architect-reviewer 에이전트의 통합 출력}
{호출되지 않은 경우: "이 변경 범위에서는 아키텍처 리뷰가 필요하지 않습니다"}

---

## ✅ 리뷰 체크리스트

### Critical (반드시 수정)

- [ ] {이슈 1}
- [ ] {이슈 2}

### Warning (수정 권장)

- [ ] {이슈 3}

### Suggestion (고려)

- [ ] {개선 1}

---

## 📝 다음 단계

{리뷰 결과에 기반한 권장 조치}
```

---

## 중요 사항

- **코드 직접 수정 금지** - 이 커맨드는 리뷰만 수행
- **에이전트 자율성**: code-reviewer와 architect-reviewer는 필요에 따라 파일 읽기, 테스트 실행, 의존성 분석 가능
- **코딩 가이드라인**: code-reviewer가 코딩 표준 위반 확인
- **점진적 리뷰**: 대규모 변경(20개 이상 파일)의 경우, 에이전트가 영향도 높은 영역을 우선 집중
- **Git 안전성**: 모든 git 명령은 읽기 전용 (status, diff, log, show)

---

## 사용 예시

```bash
# 미커밋 변경사항 리뷰
/code-review

# 특정 커밋 리뷰 (대화에서 커밋 해시 지정)
# 사용자: "커밋 abc123 리뷰해줘"
# 그 다음: /code-review
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kubrickcode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

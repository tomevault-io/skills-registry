---
name: review-translation
description: 통합 리포트 생성 스킵 (개별 리포트만) Use when this capability is needed.
metadata:
  author: team-attention
---

# review-translation Skill

번역된 한국어 콘텐츠의 품질을 3개 AI(Claude, Codex, Gemini)로 교차 검증합니다.

## 사용법

```
# 기본 (3개 AI 교차 검증)
/review-translation <week/slug>

# Claude만 사용
/review-translation <week/slug> --claude-only

# 통합 리포트 스킵
/review-translation <week/slug> --skip-integration
```

## 예시

```
# 단일 페이지
/review-translation week1/how-openai-uses-codex

# YouTube 개별 챕터
/review-translation week1/deep-dive-llms/introduction
/review-translation week1/prompt-engineering-guide/zeroshot
```

## 입출력

### 입력 파일

| 구조 | 원본 (eng) 경로 | 번역 (kr) 경로 |
|------|----------------|----------------|
| 기본 구조 | `docs/week{N}/{slug}/eng/{file}.md` | `docs/week{N}/{slug}/kr/{file}.md` |
| 레거시 구조 | `docs/week{N}/{slug}/{file}.md` | `docs/week{N}/{slug}/kr/{file}.md` |

**경로 예시:**
```
# 기본 구조 (eng/kr 분리)
week1/deep-dive-llms/introduction
→ 원본: docs/week1/deep-dive-llms/eng/introduction.md
→ 번역: docs/week1/deep-dive-llms/kr/introduction.md

week1/how-openai-uses-codex
→ 원본: docs/week1/how-openai-uses-codex/eng/*.md
→ 번역: docs/week1/how-openai-uses-codex/kr/*.md

# 레거시 구조 (eng 없이 직접)
week1/prompt-engineering-guide/zeroshot
→ 원본: docs/week1/prompt-engineering-guide/zeroshot.md
→ 번역: docs/week1/prompt-engineering-guide/kr/zeroshot.md
```

### 출력 파일

```
.claude/outputs/review-translation/
└── week{N}/{slug}/
    ├── claude-review.json      # Claude 리뷰
    ├── codex-review.json       # Codex 리뷰
    ├── gemini-review.json      # Gemini 리뷰
    └── integrated-report.md    # 통합 리포트

# 챕터별 리뷰 시
.claude/outputs/review-translation/
└── week{N}/{slug}/{chapter}/
    ├── claude-review.json
    ├── codex-review.json
    ├── gemini-review.json
    └── integrated-report.md
```

## 워크플로우

```
/review-translation week1/how-openai-uses-codex
           │
           ▼
┌──────────────────────────────────────┐
│ 1. 파일 읽기                          │
│    - 원본 마크다운                     │
│    - 번역 마크다운                     │
│    - 용어집 (참조)                     │
└──────────────────────────────────────┘
           │
           ▼
┌──────────────────────────────────────┐
│ 2. 프롬프트 생성                       │
│    external-prompt.md 템플릿 사용     │
│    변수 치환: 원본, 번역, 용어집        │
└──────────────────────────────────────┘
           │
           ▼
┌──────────────────────────────────────┐
│ 3. 병렬 AI 리뷰 실행                   │
│ ┌─────────┬─────────┬─────────┐      │
│ │ Claude  │  Codex  │ Gemini  │      │
│ │ (Task)  │ (Bash)  │ (Bash)  │      │
│ └─────────┴─────────┴─────────┘      │
│    동시 실행 (3분 타임아웃)            │
└──────────────────────────────────────┘
           │
           ▼
┌──────────────────────────────────────┐
│ 4. 개별 리포트 저장                    │
│    claude-review.json                │
│    codex-review.json                 │
│    gemini-review.json                │
└──────────────────────────────────────┘
           │
           ▼
┌──────────────────────────────────────┐
│ 5. Task: report-integrator           │
│    3개 리포트 통합                     │
│    → integrated-report.md            │
└──────────────────────────────────────┘
           │
           ▼
┌──────────────────────────────────────┐
│ 6. 결과 요약 출력                      │
│    점수, 합의/개별 이슈, 권장 조치      │
└──────────────────────────────────────┘
```

## 실행 지침

이 스킬이 호출되면 다음 단계를 따르세요.

### Step 1: 경로 파싱 및 파일 읽기

```
경로 구조 확인:
- 2단계 (week1/slug): 디렉토리 전체 또는 단일 파일
- 3단계 (week1/slug/chapter): 개별 챕터

파일 경로 결정 로직:
1. 먼저 eng 디렉토리 확인: docs/week{N}/{slug}/eng/{chapter}.md
2. eng 없으면 레거시 경로: docs/week{N}/{slug}/{chapter}.md
3. 번역은 항상: docs/week{N}/{slug}/kr/{chapter}.md

예시 - week1/deep-dive-llms/introduction:
1. docs/week1/deep-dive-llms/eng/introduction.md 확인 → 있음 ✓
2. docs/week1/deep-dive-llms/kr/introduction.md 읽기
3. docs/glossary.md 읽기 (용어집)

예시 - week1/prompt-engineering-guide/zeroshot:
1. docs/week1/prompt-engineering-guide/eng/zeroshot.md 확인 → 없음
2. docs/week1/prompt-engineering-guide/zeroshot.md 읽기 (레거시)
3. docs/week1/prompt-engineering-guide/kr/zeroshot.md 읽기
4. docs/glossary.md 읽기 (용어집)

파일이 없는 경우:
- 원본 없음: "원본 파일을 찾을 수 없습니다: {경로}" 에러
- 번역 없음: "번역 파일을 찾을 수 없습니다. /translate-reading 먼저 실행해주세요." 에러
```

### Step 2: 프롬프트 생성

```
.claude/agents/review-translation/external-prompt.md 템플릿 읽기

변수 치환:
- {{REVIEWER_NAME}}: 각 AI 이름
- {{DOCUMENT_PATH}}: 문서 경로
- {{ENGLISH_SOURCE}}: 원본 내용
- {{KOREAN_TRANSLATION}}: 번역 내용
- {{GLOSSARY}}: 용어집 내용

프롬프트를 임시 파일로 저장:
- /tmp/review-prompt-codex.txt
- /tmp/review-prompt-gemini.txt
```

### Step 3: 병렬 AI 리뷰 실행

**--claude-only가 아닌 경우**, 3개 AI를 동시에 실행합니다:

#### Claude (Task tool)
```
Task tool 호출:
- subagent_type: "general-purpose"
- prompt: .claude/agents/review-translation/claude-reviewer.md 내용
         + 원본 마크다운
         + 번역 마크다운
         + 용어집
- description: "claude-reviewer - 번역 품질 검증"
```

#### Codex (Bash tool - 동시 실행)
```bash
# 프롬프트 파일 생성
cat << 'PROMPT_EOF' > /tmp/review-prompt-codex.txt
[external-prompt.md 내용에 변수 치환]
PROMPT_EOF

# Codex 실행 (타임아웃 3분)
timeout 180 codex exec "$(cat /tmp/review-prompt-codex.txt)" \
  -o /tmp/codex-review-output.txt 2>/dev/null

# 또는 JSON 모드
timeout 180 codex exec "$(cat /tmp/review-prompt-codex.txt)" --json 2>/dev/null | \
  grep '"type":"assistant"' | tail -1 | jq -r '.message.content[0].text' \
  > /tmp/codex-review-output.txt
```

#### Gemini (Bash tool - 동시 실행)
```bash
# 프롬프트 파일 생성
cat << 'PROMPT_EOF' > /tmp/review-prompt-gemini.txt
[external-prompt.md 내용에 변수 치환]
PROMPT_EOF

# Gemini 실행 (타임아웃 3분, yolo 모드)
timeout 180 gemini "$(cat /tmp/review-prompt-gemini.txt)" -y -o json 2>/dev/null | \
  jq -r '.text' > /tmp/gemini-review-output.txt
```

### Step 4: 개별 리포트 저장

```
출력 디렉토리 생성:
mkdir -p .claude/outputs/review-translation/week{N}/{slug}/

각 AI 결과를 JSON으로 저장:
- claude-review.json
- codex-review.json (Codex 출력에서 JSON 추출)
- gemini-review.json (Gemini 출력에서 JSON 추출)

JSON 파싱 실패 시:
- 원본 출력을 {ai}-review-raw.txt로 저장
- 에러 메시지 표시 후 계속 진행
```

### Step 5: 리포트 통합 (--skip-integration이 아닌 경우)

```
Task tool 호출:
- subagent_type: "general-purpose"
- prompt: .claude/agents/review-translation/report-integrator.md 내용
         + Claude 리뷰 JSON
         + Codex 리뷰 JSON
         + Gemini 리뷰 JSON
         + 문서 경로
- description: "report-integrator - 리포트 통합"

결과: 마크다운 형식의 통합 리포트
Write tool로 integrated-report.md 저장
```

### Step 6: 결과 요약 출력

```
완료 메시지 형식 (3개 AI 모드):

✅ 번역 리뷰 완료!

📊 **종합 점수**: {overall_avg}/10

| 항목 | Claude | Codex | Gemini | 평균 |
|------|--------|-------|--------|------|
| 정확성 | {a1} | {a2} | {a3} | {avg} |
| 완전성 | {c1} | {c2} | {c3} | {avg} |
| 자연스러움 | {n1} | {n2} | {n3} | {avg} |
| 용어 일관성 | {t1} | {t2} | {t3} | {avg} |

📋 **이슈 요약**:
  - 합의된 이슈: {N}건 (Critical {N}, Major {N}, Minor {N})
  - 개별 의견: {N}건

📁 **리포트 저장 위치**:
  .claude/outputs/review-translation/week{N}/{slug}/
  ├── integrated-report.md (통합)
  ├── claude-review.json
  ├── codex-review.json
  └── gemini-review.json

💡 **권장 조치**:
  {합의된 이슈 기반 권장사항}
```

## 옵션

### --claude-only

Claude만 사용하여 리뷰합니다.
- Codex/Gemini CLI 미설치 또는 미인증 시 사용
- 통합 리포트 대신 Claude 리포트만 생성

### --skip-integration

통합 리포트 생성을 스킵합니다.
- 3개 AI 개별 리포트만 저장
- 빠른 검증이 필요할 때 사용

## Agent 파일

- `.claude/agents/review-translation/claude-reviewer.md` - Claude 리뷰 에이전트
- `.claude/agents/review-translation/external-prompt.md` - Codex/Gemini 프롬프트 템플릿
- `.claude/agents/review-translation/report-integrator.md` - 리포트 통합 에이전트

## CLI 요구사항

### Codex CLI
```bash
npm install -g @openai/codex
codex login  # 브라우저 OAuth 인증
```

### Gemini CLI
```bash
npm install -g @google/gemini-cli
gemini  # "Login with Google" 선택
```

## 에러 처리

| 상황 | 처리 |
|------|------|
| 원본 파일 없음 | "원본 파일을 찾을 수 없습니다: {경로}" |
| 번역 파일 없음 | "번역 파일을 찾을 수 없습니다. /translate-reading 먼저 실행해주세요." |
| Codex CLI 미설치 | `which codex` 확인 → Claude만 실행 |
| Gemini CLI 미설치 | `which gemini` 확인 → Claude만 실행 |
| CLI 인증 실패 | "인증 필요: codex login / gemini 재실행" |
| JSON 파싱 실패 | 원본 출력 저장 후 계속 진행 |
| 타임아웃 (3분) | 해당 AI 스킵 후 계속 진행 |

## 점수 기준

| 점수 | 의미 |
|------|------|
| 9-10 | 출판 수준, 수정 불필요 |
| 7-8 | 양호, Minor 수정 권장 |
| 5-6 | 보통, Major 수정 필요 |
| 3-4 | 미흡, 상당 부분 재번역 권장 |
| 1-2 | 불량, 전체 재번역 필요 |

## 향후 확장 (Phase 4)

- `--all-chapters`: 전체 챕터 일괄 리뷰
- `master-report.md`: 챕터별 요약 포함 마스터 리포트
- translate-reading 연계: `--review` 옵션으로 자동 검증

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/team-attention) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

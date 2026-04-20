---
name: analyze-project
description: 로컬 경로 또는 GitHub URL의 프로젝트를 분석하여 _projects/ 에 MDX 콘텐츠를 작성합니다. Use when this capability is needed.
metadata:
  author: anveloper
---

# 프로젝트 분석 및 콘텐츠 작성

분석 대상: `$ARGUMENTS`

## 0단계: 대상 경로 결정

인자가 GitHub URL인지 로컬 경로인지 판별합니다.

### GitHub URL 판별 규칙

다음 패턴 중 하나에 해당하면 GitHub URL로 간주:

- `https://github.com/<owner>/<repo>`
- `github.com/<owner>/<repo>`
- `<owner>/<repo>` (슬래시 1개, 로컬 경로에 해당하는 파일/디렉토리가 없는 경우)

### GitHub URL인 경우

```bash
# /tmp 하위에 클론
REPO_DIR=$(mktemp -d /tmp/analyze-project-XXXXXX)
gh repo clone <owner>/<repo> "$REPO_DIR" -- --depth 1
```

- 클론된 `$REPO_DIR`을 이후 단계에서 프로젝트 경로로 사용
- GitHub URL에서 `<owner>/<repo>` 추출하여 frontmatter의 `github` 필드에 자동 기입
- 분석 완료 후 5단계에서 `$REPO_DIR` 삭제

### 로컬 경로인 경우

- `$ARGUMENTS`를 그대로 프로젝트 경로로 사용

> 이하 단계에서 `$PROJECT_PATH`는 위에서 결정된 실제 경로를 의미합니다.

## 1단계: 대상 프로젝트 탐색

결정된 프로젝트 경로를 전체적으로 탐색합니다.

### 1.1 기본 구조 파악

```bash
# 디렉토리 구조 확인 (2단계 깊이)
ls -la $PROJECT_PATH
find $PROJECT_PATH -maxdepth 2 -type f | head -50
```

### 1.2 프로젝트 설정 파일 확인

우선순위대로 읽기:

| 파일                                 | 확인 내용                             |
| ------------------------------------ | ------------------------------------- |
| `package.json`                       | 프로젝트명, 설명, 기술 스택, 스크립트 |
| `README.md`                          | 프로젝트 소개, 기능 설명              |
| `tsconfig.json` / `jsconfig.json`    | 언어 설정                             |
| `Dockerfile`, `docker-compose.yml`   | 배포 환경                             |
| `.env.example`                       | 환경 변수 구성                        |
| `build.gradle`, `pom.xml`            | Java/Kotlin 프로젝트                  |
| `requirements.txt`, `pyproject.toml` | Python 프로젝트                       |
| `go.mod`                             | Go 프로젝트                           |

### 1.3 소스코드 구조 분석

```bash
# 주요 소스 디렉토리 파악
ls -la $PROJECT_PATH/src/ 2>/dev/null
ls -la $PROJECT_PATH/app/ 2>/dev/null
ls -la $PROJECT_PATH/lib/ 2>/dev/null
```

핵심 파일 탐색:

- 진입점 파일 (index, main, app)
- 라우팅/API 구조
- 주요 컴포넌트/모듈 구조
- 설정 파일

### 1.4 문서 탐색

```bash
# 문서 파일 찾기
find $PROJECT_PATH -name "*.md" -maxdepth 3 | head -20
find $PROJECT_PATH/docs -type f 2>/dev/null | head -20
```

## 2단계: 분석 정보 정리

탐색한 내용을 기반으로 다음 항목을 정리합니다:

### 필수 항목

| 항목          | 설명                                 |
| ------------- | ------------------------------------ |
| 프로젝트명    | 공식 이름                            |
| 한줄 소개     | 프로젝트 핵심 설명 (1문장)           |
| 기술 스택     | 언어, 프레임워크, 라이브러리, DB 등  |
| 주요 기능     | 핵심 기능 3-5개                      |
| 프로젝트 구조 | 디렉토리/모듈 구조 요약              |
| 기간          | 개발 기간 (커밋 이력 또는 문서 기반) |

### 선택 항목

| 항목       | 설명             |
| ---------- | ---------------- |
| 아키텍처   | 전체 시스템 구조 |
| 담당 역할  | 본인 기여 부분   |
| 성과/결과  | 정량적 성과      |
| 트러블슈팅 | 해결한 주요 문제 |
| GitHub URL | 저장소 링크      |
| Demo URL   | 배포 URL         |

## 3단계: 사용자 확인

분석 결과를 요약하여 사용자에게 보여주고, 다음을 확인합니다:

- 분석 내용이 정확한지
- 추가/수정할 내용이 있는지
- 강조하고 싶은 포인트가 있는지
- slug 이름 (파일명)

## 4단계: MDX 콘텐츠 작성

`_projects/<slug>.mdx` 파일을 작성합니다.

### Frontmatter 형식

```yaml
---
title: "<프로젝트명>"
date: "<YYYY-MM-DD>"
description: "<한줄 소개>"
tags: ["<기술1>", "<기술2>", ...]
github: "<GitHub URL>" # 선택
demo: "<Demo URL>" # 선택
---
```

### 본문 구조

```markdown
# <프로젝트명>

## 개요

<프로젝트 소개 2-3문장>

## 주요 기능

- **기능 1**: 설명
- **기능 2**: 설명
- **기능 3**: 설명

## 기술 스택

| 분류     | 기술 |
| -------- | ---- |
| Frontend | ...  |
| Backend  | ...  |
| Database | ...  |
| Infra    | ...  |

## 프로젝트 구조

주요 모듈/디렉토리 구조 설명

## 담당 역할

- 역할 1
- 역할 2

## 트러블슈팅

### 문제 1: <제목>

- **상황**: ...
- **원인**: ...
- **해결**: ...

## 회고

프로젝트를 통해 배운 점, 개선할 점
```

### 작성 규칙

- 한국어로 작성
- 기술 용어는 영문 유지 (React, TypeScript 등)
- 과장 없이 사실 기반으로 작성
- 코드 블록은 실제 프로젝트의 코드 참조
- 불필요한 세부사항 생략, 핵심만 간결하게
- **MDX 파싱 안전성**: 코드 블록 외부에서 `<`, `>`, `{`, `}` 문자는 반드시 백틱(`` ` ``)으로 감싸거나 `\`로 이스케이프 (예: `Map<String, List>` → `` `Map<String, List>` ``)

## 5단계: 이미지 디렉토리 생성

생성된 프로젝트의 slug에 맞는 이미지 디렉토리를 생성합니다:

```bash
mkdir -p public/images/projects/<slug>/
```

## 6단계: 결과 보고

GitHub URL로 클론한 경우 임시 디렉토리를 삭제합니다:

```bash
# GitHub 클론인 경우에만 실행
rm -rf $REPO_DIR
```

```
생성된 파일: _projects/<slug>.mdx
프로젝트명: <name>
기술 스택: <techs>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anveloper) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

---
name: fai-skill
description: description: FAI(Family AI) 프로젝트 관리 스킬. Dart/Flutter 학습 전용 소규모 LLM 개발 프로젝트. 다음 상황에서 사용: (1) "/fai" 명령 실행 시, (2) FAI 프로젝트 전반 관리 요청 시, (3) 학습 데이터 준비/처리 요청 시, (4) 모델 학습/생성 관련 작업 시, (5) 분산 학습 시스템(distributed training) 관련 작업 시, (6) Coordinator 서버/Worker 클라이언트 개발 시, (7) FedAvg 병합/검증/스케줄링 관련 작업 시. Use when this capability is needed.
metadata:
  author: thruthesky
---
---
name: fai-skill
description: FAI(Family AI) 프로젝트 관리 스킬. Dart/Flutter 학습 전용 소규모 LLM 개발 프로젝트. 다음 상황에서 사용: (1) "/fai" 명령 실행 시, (2) FAI 프로젝트 전반 관리 요청 시, (3) 학습 데이터 준비/처리 요청 시, (4) 모델 학습/생성 관련 작업 시, (5) 분산 학습 시스템(distributed training) 관련 작업 시, (6) Coordinator 서버/Worker 클라이언트 개발 시, (7) FedAvg 병합/검증/스케줄링 관련 작업 시.
---

# FAI (Family AI) 프로젝트 관리 스킬

## 프로젝트 개요

FAI는 Dart와 Flutter 개발 학습 정보를 제공하는 소규모 스터디 LLM을 처음부터(from scratch) 구현하는 프로젝트이다.

- **공식 명칭**: FAI (Family AI)
- **분류**: Flutter Study GPT, Flutter LM, Dart/Flutter Learning Model
- **목적**: 파인튜닝이 아닌, 토크나이저부터 GPT 모델까지 직접 구현

---

## ⚠️ ENGLISH-ONLY RULE (영어 전용 규칙) ⚠️

### 🚨 절대 규칙: 모든 학습 데이터와 모델 출력은 100% 영어로만 작성

| 항목 | 규칙 |
|------|------|
| **학습 데이터** | English only. No Korean. |
| **모델 응답** | English only. No Korean. |
| **코드 주석** | English only. No Korean. |
| **data/raw/**/*.md** | English only. No Korean. |
| **data/samples/**/*.txt** | English only. No Korean. |

### 왜 영어 전용인가?

1. **토큰 효율성**: 한글은 토큰당 정보량이 낮음 (한 글자 = 여러 토큰)
2. **어휘 크기 최적화**: 영어는 더 적은 vocab_size로 효과적 학습 가능
3. **공식 문서 일관성**: Flutter/Dart 공식 문서가 영어로 작성됨
4. **모델 품질**: 소규모 모델에서 단일 언어가 더 나은 성능 발휘

---

## 데이터 파이프라인 (2단계 분리)

### 개요

```
[Stage 1: 정보 수집]          [Stage 2: 전처리]              [Stage 3: 학습]
인터넷 검색                    Markdown → 학습 형식            토크나이저 → GPT
     ↓                              ↓                            ↓
data/raw/**/*.md        →    data/samples/**/*.txt    →    train.bin/val.bin
```

---

### Stage 1: 정보 수집 (search-skill 사용)

**목적**: 인터넷에서 Dart/Flutter 정보를 검색하여 원본 Markdown으로 저장

**저장 위치**: `data/raw/<도메인>/**/*.md`

**URL → 파일 경로 매핑 규칙**:

| URL | 파일 경로 |
|-----|----------|
| `https://dart.dev/` | `data/raw/dart.dev/index.md` |
| `https://dart.dev/overview` | `data/raw/dart.dev/overview.md` |
| `https://dart.dev/language` | `data/raw/dart.dev/language.md` |
| `https://dart.dev/language/variables` | `data/raw/dart.dev/language/variables.md` |
| `https://docs.flutter.dev/` | `data/raw/docs.flutter.dev/index.md` |
| `https://docs.flutter.dev/ui/widgets` | `data/raw/docs.flutter.dev/ui/widgets.md` |
| `https://api.flutter.dev/flutter/widgets/StatefulWidget-class.html` | `data/raw/api.flutter.dev/flutter/widgets/StatefulWidget-class.md` |

**수집 대상 사이트**:

| 사이트 | 설명 | 저장 경로 |
|--------|------|----------|
| dart.dev | Dart 언어 공식 문서 | `data/raw/dart.dev/` |
| docs.flutter.dev | Flutter 공식 문서 | `data/raw/docs.flutter.dev/` |
| api.flutter.dev | Flutter API 레퍼런스 | `data/raw/api.flutter.dev/` |
| api.dart.dev | Dart API 레퍼런스 | `data/raw/api.dart.dev/` |
| pub.dev | 패키지 문서 | `data/raw/pub.dev/` |

**Stage 1 파일 형식** (원본 그대로):

```markdown
# Page Title

(원본 문서 내용을 Markdown으로 변환하여 저장)

## Source
- URL: https://dart.dev/language/variables
- Fetched: 2024-01-27
```

---

### Stage 2: 전처리 (raw → samples)

**목적**: 원본 Markdown을 학습용 Q&A 형식으로 변환

**저장 위치**: `data/samples/<도메인>/**/*.txt`

**매핑 규칙**:

| 원본 파일 | 전처리 파일 |
|----------|------------|
| `data/raw/dart.dev/language/variables.md` | `data/samples/dart.dev/language/variables.txt` |
| `data/raw/docs.flutter.dev/ui/widgets.md` | `data/samples/docs.flutter.dev/ui/widgets.txt` |

**Stage 2 파일 형식** (학습용):

```
[QUESTION]
What are variables in Dart and how do you declare them?
[/QUESTION]

[DOC]
(data/raw/dart.dev/language/variables.md 내용 발췌)
[/DOC]

[ANSWER]
Summary:
- Variables store references to values in Dart
- Use var, final, const, or explicit types for declaration

Learning Checklist:
- Prerequisites: Basic programming concepts
- Learning Goals: Understand variable declaration and initialization

Code Example:
```dart
// Variable declarations in Dart
var name = 'Flutter';      // Type inferred as String
String language = 'Dart';  // Explicit type
final version = 3.0;       // Runtime constant
const pi = 3.14159;        // Compile-time constant
```

Related APIs:
- Object class
- Type system

References:
- https://dart.dev/language/variables

Detailed Explanation:
Dart supports several ways to declare variables...
[/ANSWER]
```

---

### Stage 3: 학습 준비

**최종 통합 및 바이너리 변환**:

```bash
# samples 폴더의 모든 .txt 파일을 통합
uv run python scripts/prepare_samples.py      # → data/samples.txt

# 토크나이저 학습
uv run python scripts/train_tokenizer.py      # → data/tokenizer.json

# 바이너리 변환
uv run python scripts/build_bin_dataset.py    # → data/train.bin, val.bin

# GPT 학습
uv run python scripts/train_gpt.py            # → checkpoints/ckpt.pt
```

---

## 프로젝트 구조

```
fai/
├── data/
│   ├── raw/                      # Stage 1: 원본 Markdown (사이트별)
│   │   ├── dart.dev/
│   │   ├── docs.flutter.dev/
│   │   └── api.flutter.dev/
│   ├── samples/                  # Stage 2: 전처리된 학습 데이터
│   ├── samples.txt               # Stage 3: 통합 학습 데이터
│   ├── tokenizer.json
│   ├── train.bin
│   └── val.bin
├── scripts/
│   ├── prepare_samples.py        # samples/**/*.txt → samples.txt
│   ├── train_tokenizer.py
│   ├── build_bin_dataset.py
│   ├── train_gpt.py              # 단독 학습 (로컬)
│   └── generate.py
├── distributed/                   # ★ 분산 학습 시스템
│   ├── common/                    # 서버+워커 공통 (model, constants, protocol)
│   ├── server/                    # Coordinator 서버 (FastAPI + FedAvg)
│   │   ├── routes/                # API 엔드포인트 (15개)
│   │   └── services/              # heartbeat, merger, validator, scheduler
│   └── worker/                    # 학습 워커 (CLI + trainer)
├── docker/                        # Docker Compose 구성
│   ├── Dockerfile
│   └── docker-compose.yml
├── distributed-training-plan.md   # 분산 학습 설계 문서 (17섹션)
├── checkpoints/
└── docs/
```

---

## 하이퍼파라미터 (M4 기준)

| 파라미터 | 값 | 설명 |
|----------|-----|------|
| vocab_size | 24,000 | 영어 전용으로 최적화된 크기 |
| block_size | 256 | 컨텍스트 길이 |
| n_layer | 6 | Transformer 블록 수 |
| n_head | 6 | Attention Head 수 |
| n_embd | 384 | 임베딩 차원 |
| batch_size | 16 | 배치 크기 |

---

## 분산 학습 시스템

자발적 참여자들이 GPU/CPU를 제공하여 FAI GPT 모델을 협업 학습하는 FedAvg 기반 비동기 분산 학습 시스템.

**핵심 흐름**: Worker 등록 → 작업 요청 → 체크포인트 다운로드 → 로컬 N스텝 학습 → 가중치 업로드 → FedAvg 병합

**실행 방법**:

```bash
# Coordinator 서버
uv run uvicorn distributed.server.app:app --host 0.0.0.0 --port 8000

# 워커 실행
uv run python -m distributed.worker --name "이름" --server http://localhost:8000

# Docker (서버 + 워커 3대)
cd docker && docker compose up --scale worker=3
```

**상세 아키텍처, API 목록, 핵심 코드**: [distributed-training.md](references/distributed-training.md) 참조

---

## 관련 스킬

- **search-skill**: Stage 1 (정보 수집) 실행용 - `/search <키워드>`
- **fai-skill**: 전체 프로젝트 관리 - `/fai`

## 관련 문서

- **기술 문서**: `docs/00-overview.md` ~ `docs/08-server.md`
- **분산 학습 설계**: `distributed-training-plan.md` (17섹션, ~2100줄)
- **분산 학습 레퍼런스**: [references/distributed-training.md](references/distributed-training.md)
- **FAQ**: `faq/` 폴더 내 개별 문서
- **학습 가이드**: `study.md`, `step-by-step.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thruthesky) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

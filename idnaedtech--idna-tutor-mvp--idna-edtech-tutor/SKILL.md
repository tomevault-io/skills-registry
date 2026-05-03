---
name: idna-edtech-tutor
description: > Use when this capability is needed.
metadata:
  author: idnaedtech
---

# IDNA EdTech — Voice Tutor Development Skill (v4.0.0)

## 1. What is IDNA

IDNA EdTech builds "Didi" (दीदी), an AI-powered multilingual voice tutor for
Class 6-10 students across India. 33+ boards, 22 languages, 10 class levels.

## 2. Current Status (v10.26.2)

| Item | Status |
|------|--------|
| Version | **v10.26.2 LIVE on Railway** |
| URL | https://didi.idnaedtech.com |
| Tests | **769 test functions** across ~20 test files |
| Math Content | ALL 13 NCERT Class 8 chapters: 349 active Qs, 193 concepts |
| Science Content | ALL 13 NCERT Class 8 chapters: 185 concepts, 195 MCQs |
| Languages | Hindi, English, Hinglish, Telugu + 8 more via Sarvam |
| Subjects | Mathematics (full), Science (concept teaching live) |
| Phase | **P1 ACTIVE — Content expansion + multi-subject** |

## 3. Tech Stack (v10.26.x)

| Layer | Technology | Notes |
|-------|-----------|-------|
| Teaching LLM | **Claude Sonnet 4.6** | Anthropic. System prompt is top-level param. |
| Classifier LLM | **Claude Haiku 4.5** | 10 categories + inline eval. JSON via prompt. |
| Content Pipeline | **Claude Opus 4.6** | Structuring + humanizing in idna-content repo. |
| STT | **Sarvam Saaras v3** | 22 languages, transcribe mode. |
| TTS | Sarvam Bulbul v3 | Speaker: simran, hi-IN, pace 0.85 |
| Backend | FastAPI (Python 3.11) | Async endpoints |
| Frontend | HTML/JS | student.html with SyncEngine + KaTeX board |
| Database | PostgreSQL (Railway) | chapters + content_units tables |
| Hosting | Railway | Auto-deploy from GitHub main |

**Two repos:**  (app) +  (curriculum JSON)

## 4. Key Architecture

- **Dual pipeline:** streaming (SSE) + non-streaming (JSON). Both use same instruction_builder.
- **v7.3 FSM:** 6 states x 10 categories. Returns Action objects.
- **Subject-aware DIDI_BASE:** {subject_name} placeholder adapts to Math/Science/etc.
- **Content DB:** chapters + content_units tables. content_repository.py single access layer.
- **Textbook resolver:** 25+ Indian boards mapped. All queries filter by textbook.
- **SyncEngine:** Segment-based board sync with timing metadata (ITE Phase A).

## 5. Voice Pipeline



## 6. Key Features (v10.8-v10.26)

- **AI Migration (v10.26.0):** OpenAI -> Anthropic Claude, Saarika -> Saaras v3
- **Subject Switch (v10.25.1):** detect_subject_request() + _handle_subject_switch(), 7 subjects
- **ITE Phase A (v10.25.0):** SyncEngine, 64 teaching scripts, segment-based board sync
- **Teaching Board (v10.11.0):** Split-screen KaTeX, 7 renderers, 108+ board visuals
- **Student Memory (v10.10.0):** Cross-session persistence, periodic save
- **Content DB (v10.21.0):** PostgreSQL content_units, content_repository.py
- **Homework Help (v10.19.0):** LLM-detected, any subject/language
- **22 Languages (v10.20.0):** language_config.py single source of truth
- **ALL 13 Math + 13 Science chapters complete**

## 7. Development Rules

1. Run tests + verify.py before every commit
2. Never change TTS voice (Sarvam simran only)
3. Every _sys() call needs session_context=ctx, question_data=q
4. Anthropic API: system is top-level param, response.content[0].text
5. Pre-flight protocol: explain root cause, files, verification. Wait for CEO approval.
6. Never create app models directory (shadows app models.py)

## 8. Content Pipeline (idna-content repo)

Workflow: add JSON -> validate.py -> push -> CEO runs publish.py with DATABASE_URL
Pipeline uses Claude Opus 4.6 for structuring and humanizing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/idnaedtech) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

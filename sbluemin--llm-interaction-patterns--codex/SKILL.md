---
name: codex
description: Delegate prompts to OpenAI Codex CLI Use when this capability is needed.
metadata:
  author: sbluemin
---

# Codex CLI Skill

OpenAI Codex CLI를 사용하여 작업을 수행합니다.

## 1. 사용 가능한 모델

### 1.1. CLI가 제공하는 모델

- `gpt-5.2` **기본 모델**

## 2. 사용 방법

CLI를 비대화형 모드(`exec`)로 실행하며, 승인 절차를 건너뛰는 모드(`--full-auto` 또는 `--dangerously-bypass-approvals-and-sandbox`)를 사용할 수 있습니다.

### 2.1. 웹 검색 기능

웹 검색을 사용하려면 `--enable web_search_request` 옵션을 추가해야 합니다.

> **참고**: `web_search`는 deprecated 되었으며, `web_search_request`를 사용해야 합니다.

### 2.2. 예시

```bash
# 기본 사용
codex exec "여기에 프롬프트 입력" --full-auto

# 모델 지정 사용
codex exec "여기에 프롬프트 입력" --model "o1" --full-auto

# 웹 검색 활성화
codex --enable web_search_request exec "여기에 프롬프트 입력" --full-auto

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sbluemin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

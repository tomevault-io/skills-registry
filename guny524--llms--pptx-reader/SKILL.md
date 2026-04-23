---
name: pptx-reader
description: | Use when this capability is needed.
metadata:
  author: guny524
---

# PPTX Reader

anthropic `document-skills:pptx` skill의 Python 실행 환경.

## 사용법

```bash
~/llms/skills/pptx-reader/.venv/bin/python -m markitdown <pptx_file>
```

## 설치된 패키지

- markitdown[pptx]: pptx -> markdown 변환
- python-pptx: pptx 파일 처리
- 기타 의존성 (lxml, pillow, etc.)

## 환경 정보

- Python: 3.11
- venv 경로: `~/llms/skills/pptx-reader/.venv`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guny524) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

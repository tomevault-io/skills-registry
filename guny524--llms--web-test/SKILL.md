---
name: web-test
description: 웹 페이지 구조 분석 및 E2E 테스트. HTML/XPath 구조 파악, 스크린샷, 브라우저 자동화. Playwright 기반 스크립트 제공. 웹 컴포넌트 테스트, 웹 스크래핑, DOM 구조 분석 시 사용. Use when this capability is needed.
metadata:
  author: guny524
---

# Web Test

Playwright 기반 웹 테스트 스크립트

## 1. 전제조건

```bash
# Playwright 설치 (최초 1회)
pip install playwright
playwright install chromium
```

## 2. 스크립트 목록

| 스크립트 | 용도 |
|---------|------|
| get-page-html.py | 페이지 전체 HTML 가져오기 |
| get-dom-structure.py | DOM 구조 트리 출력 |
| find-elements.py | CSS/XPath로 요소 찾기 |
| take-screenshot.py | 페이지 스크린샷 |
| fill-form.py | 폼 자동 입력 |

## 3. 사용 예시

### 3-1. 페이지 HTML 가져오기
```bash
python ~/llms/skills/web-test/scripts/get-page-html.py "https://example.com"
```

### 3-2. DOM 구조 분석
```bash
python ~/llms/skills/web-test/scripts/get-dom-structure.py "https://example.com" --depth 3
```

### 3-3. 요소 찾기
```bash
# CSS selector
python ~/llms/skills/web-test/scripts/find-elements.py "https://example.com" --css "button.submit"

# XPath
python ~/llms/skills/web-test/scripts/find-elements.py "https://example.com" --xpath "//button[@type='submit']"
```

### 3-4. 스크린샷
```bash
python ~/llms/skills/web-test/scripts/take-screenshot.py "https://example.com" -o screenshot.png
```

### 3-5. 폼 입력
```bash
python ~/llms/skills/web-test/scripts/fill-form.py "https://example.com/login" \
  --field "input[name=email]" "test@example.com" \
  --field "input[name=password]" "password123" \
  --submit "button[type=submit]"
```

## 4. 공통 옵션

모든 스크립트 공통:
- `--headless` / `--no-headless`: 헤드리스 모드 (기본: headless)
- `--timeout <ms>`: 타임아웃 (기본: 30000)
- `--wait <selector>`: 특정 요소 대기 후 실행
- `-h, --help`: 도움말

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guny524) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

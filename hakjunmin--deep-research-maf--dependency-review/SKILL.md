---
name: dependency-review
description: Python 프로젝트 의존성 관리 및 검토 가이드 Use when this capability is needed.
metadata:
  author: hakjunmin
---

# Dependency Review Skill

## Overview
Python 프로젝트의 의존성 패키지 관리, 취약점 검토, 업데이트 및 최적화 가이드

## Dependency Management Tools

### 1. Pip-audit
PyPI 패키지 보안 감사

```bash
uv add --dev pip-audit

# 현재 환경 검사
pip-audit

# 심각도 레벨 설정
pip-audit --vulnerability-service osv --severity HIGH

# 자동 수정 (가능한 경우)
pip-audit --fix

# requirements.txt 검사
pip-audit -r requirements.txt

# 특정 패키지 제외
pip-audit --ignore-vuln PYSEC-2023-XXX
```

### 2. Safety
Known Security Vulnerabilities 체크

```bash
uv add --dev safety

# 설치된 패키지 검사
safety check

# requirements.txt 검사
safety check -r requirements.txt

# JSON 출력
safety check --json

# 상세 보고서
safety check --full-report

# 특정 취약점 무시
safety check --ignore 12345
```

### 3. Pipdeptree
의존성 트리 시각화

```bash
uv add --dev pipdeptree

# 의존성 트리 출력
pipdeptree

# 역방향 의존성 (어떤 패키지가 이것에 의존하는가?)
pipdeptree -r -p requests

# JSON 출력
pipdeptree --json

# 그래프 생성
pipdeptree --graph-output png > dependencies.png

# 특정 패키지만
pipdeptree -p fastapi
```

### 4. UV (권장)
Rust 기반의 빠른 Python 패키지 관리자 - 의존성 관리, 잠금, 동기화

```bash
# 프로젝트 초기화
uv init

# 패키지 추가
uv add fastapi uvicorn pydantic

# 개발 의존성 추가
uv add --dev pytest ruff mypy

# 의존성 설치/동기화
uv sync

# 의존성 업데이트
uv lock --upgrade

# 특정 패키지만 업데이트
uv add fastapi@latest

# pyproject.toml과 uv.lock 자동 관리
```

#### pyproject.toml (UV 사용)
```toml
[project]
name = "deep-research-maf"
version = "1.0.0"
requires-python = ">=3.12"
dependencies = [
    "fastapi>=0.104.0",
    "uvicorn[standard]>=0.25.0",
    "pydantic>=2.0",
    "python-dotenv",
    "openai>=1.0.0",
]

[dependency-groups]
dev = [
    "pytest>=7.4.0",
    "ruff>=0.1.0",
    "mypy>=1.7.0",
]
```

#### uv.lock (자동 생성)
- 모든 의존성 버전 잠금
- 크로스 플랫폼 재현성 보장
- Git에 커밋 권장

### 5. UV vs Poetry vs Pip

| Feature | UV (권장) | Poetry | Pip |
|---------|----------|---------|-----|
| 속도 | ⚡️ 매우 빠름 | 보통 | 보통 |
| 의존성 잠금 | ✅ uv.lock | ✅ poetry.lock | ❌ (pip-tools 필요) |
| pyproject.toml | ✅ 표준 | ✅ 비표준 | ❌ |
| 가상환경 관리 | ✅ 자동 | ✅ 자동 | ❌ 수동 |
| 패키지 빌드 | ✅ | ✅ | ❌ |
| 취약점 검사 | 외부 도구 | 외부 도구 | 외부 도구 |

**UV 사용 권장 이유:**
- Rust로 작성되어 pip보다 10-100배 빠름
- PEP 표준 pyproject.toml 사용
- 간단한 명령어 구조
- 의존성 자동 해결 및 잠금

```bash
# UV 설치
curl -LsSf https://astral.sh/uv/install.sh | sh

# 프로젝트 설정
uv init
uv add fastapi uvicorn
uv sync

# 개발 환경
uv add --dev pytest ruff

# 실행
uv run uvicorn main:app
```

## Dependency Analysis

### 1. License Compliance

```bash
uv add --dev pip-licenses

# 라이선스 확인
pip-licenses

# 상세 정보
pip-licenses --with-urls --with-description

# CSV 출력
pip-licenses --format=csv --output-file=licenses.csv

# 특정 라이선스만
pip-licenses --filter-by-license="MIT"
```

**주요 라이선스 호환성:**
- ✅ MIT, Apache 2.0, BSD: 상업적 사용 가능
- ⚠️ LGPL: 동적 링크 시 가능
- ❌ GPL: 상업적 사용 시 전체 코드 공개 필요

### 2. Outdated Packages

```bash
# UV로 오래된 패키지 확인
uv tree --outdated

# 또는 pip list
pip list --outdated

# 특정 패키지 버전 확인
pip show fastapi

# 모든 패키지 최신 버전으로 업데이트 (주의!)
pip list --outdated --format=json | jq -r '.[] | .name' | xargs -n1 pip install -U
```

### 3. Dependency Conflicts

```bash
# UV로 의존성 충돌 확인
uv sync  # 자동으로 충돌 해결 시도

# pip check
pip check

# pipdeptree로 상세 확인
pipdeptree --warn conflict

# 역방향 의존성 확인
pipdeptree -r -p package-name
```

### 4. Unused Dependencies

```bash
uv add --dev pipreqs

# 실제 사용 중인 패키지만 추출
pipreqs backend/src --force

# 차이 확인
diff requirements.txt backend/src/requirements.txt
```

## Dependency Pinning Strategies

### UV를 사용하는 경우 (권장)

```toml
# pyproject.toml - 느슨한 버전 명시
[project]
dependencies = [
    "fastapi>=0.104.0",
    "uvicorn>=0.25.0",
]

# uv.lock - 정확한 버전 잠금 (자동 생성)
# Git에 커밋하여 재현성 보장
```

**장점:**
- pyproject.toml에는 최소 버전만 명시
- uv.lock에 정확한 버전 자동 고정
- `uv sync`로 일관된 환경 보장
- `uv lock --upgrade`로 안전하게 업데이트

### requirements.txt를 사용하는 경우

#### 1. Exact Pinning (Reproducible)
```txt
# requirements.txt
fastapi==0.104.1
uvicorn==0.25.0
```

**장점:** 완벽한 재현성
**단점:** 보안 패치 누락 가능

### 2. Compatible Release (~=)
```txt
# requirements.txt
fastapi~=0.104.0  # >=0.104.0, <0.105.0
uvicorn~=0.25.0   # >=0.25.0, <0.26.0
```

**장점:** 마이너 업데이트 자동 적용
**단점:** 예상치 못한 변경 가능

### 3. Minimum Version (>=)
```txt
# requirements.txt
fastapi>=0.104.0
uvicorn>=0.25.0
```

**장점:** 최신 기능 사용 가능
**단점:** 호환성 문제 발생 가능

### 4. Recommended: Two-file Approach

```txt
# requirements.in (loose)
fastapi>=0.104.0
uvicorn[standard]>=0.25.0

# requirements.txt (pinned, from pip-compile)
fastapi==0.104.1
uvicorn==0.25.0
click==8.1.7
...
```

## Vulnerability Management

### 1. Automated Scanning

```yaml
# .github/workflows/dependency-review.yml
name: Dependency Review

on:
  pull_request:
  schedule:
    - cron: '0 0 * * 0'  # Weekly

jobs:
  dependency-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Install UV
        run: curl -LsSf https://astral.sh/uv/install.sh | sh
      
      - name: Sync dependencies
        run: uv sync --dev
      
      - name: Dependency Review
        uses: actions/dependency-review-action@v3
        with:
          fail-on-severity: moderate
      
      - name: Pip Audit
        run: |
          uv add --dev pip-audit
          pip-audit
      
      - name: Safety Check
        run: |
          uv add --dev safety
          safety check --json
```

### 2. Dependabot Configuration

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "pip"
    directory: "/backend"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 10
    reviewers:
      - "your-team"
    labels:
      - "dependencies"
      - "python"
    versioning-strategy: increase
    ignore:
      # Major version updates
      - dependency-name: "*"
        update-types: ["version-update:semver-major"]
```

### 3. Manual Review Process

```bash
#!/bin/bash
# dependency-review.sh

echo "📦 Dependency Review Starting..."

# 1. Check for outdated packages
echo "\n1️⃣ Checking outdated packages..."
pip list --outdated

# 2. Security vulnerabilities
echo "\n2️⃣ Scanning for vulnerabilities..."
pip-audit

# 3. License compliance
echo "\n3️⃣ Checking licenses..."
pip-licenses --format=markdown

# 4. Dependency conflicts
echo "\n4️⃣ Checking for conflicts..."
pip check

# 5. Dependency tree
echo "\n5️⃣ Dependency tree..."
pipdeptree --warn conflict

echo "\n✅ Review complete!"
```

## Update Strategies

### 1. Safe Update Process (UV 사용)

```bash
# 1. 현재 상태 백업 (Git이 있다면 자동)
cp uv.lock uv.lock.backup

# 2. 오래된 패키지 확인
uv tree --outdated

# 3. 하나씩 업데이트
uv add fastapi@latest

# 4. 테스트
uv run pytest

# 5. 성공하면 커밋
git add pyproject.toml uv.lock
git commit -m "chore: update fastapi"

# 6. 실패하면 롤백
cp uv.lock.backup uv.lock
uv sync
```

### 2. Batch Update (UV)

```bash
# 모든 의존성 업데이트
uv lock --upgrade

# 동기화
uv sync

# 테스트 실행
uv run pytest

# 성공하면 커밋
git add pyproject.toml uv.lock
git commit -m "chore: update all dependencies"
```

### 3. Security-only Updates

```bash
# 취약점 보고서 획득
uv add --dev pip-audit
pip-audit --format json > vulns.json

# 취약한 패키지만 수정
pip-audit --fix

# 검증
pip-audit
```

## Dependency Optimization

### 1. Remove Unused Dependencies

```bash
# UV를 사용하는 경우 - pyproject.toml에서 제거
uv remove <unused-package>

# 또는 실제 사용 패키지 확인
uv add --dev pipreqs
pipreqs backend/src --force --savepath actual_requirements.txt

# pyproject.toml과 비교 후 수동 제거
uv remove <unused-package>
```

### 2. Minimize Dependencies

```python
# ❌ Bad: Heavy dependency for simple task
import pandas as pd
df = pd.DataFrame([1, 2, 3])

# ✅ Good: Use standard library
data = [1, 2, 3]
```

### 3. Alternative Packages

Consider lighter alternatives:
- `httpx` instead of `requests` (async support)
- `orjson` instead of `json` (faster)
- `uvloop` instead of asyncio (faster)

## Best Practices

### 1. Version Control

```bash
# ✅ UV 사용 시 - 둘 다 커밋
git add pyproject.toml uv.lock
git commit -m "deps: update dependencies"

# ✅ uv.lock은 반드시 Git에 포함
# - 재현 가능한 빌드 보장
# - 크로스 플랫폼 호환성
```

### 2. Development vs Production

```toml
# pyproject.toml
[project]
dependencies = [
    "fastapi>=0.104.0",
    "uvicorn[standard]>=0.25.0",
]

[dependency-groups]
dev = [
    "pytest>=7.4.0",
    "pytest-asyncio>=0.21.0",
    "ruff>=0.1.0",
    "mypy>=1.7.0",
]
```

```bash
# Production 설치 (개발 의존성 제외)
uv sync --no-dev

# Development 설치 (모든 의존성)
uv sync --dev

# 또는
uv sync  # 기본적으로 dev 포함
```

### 3. Docker Optimization

```dockerfile
# UV를 사용한 최적화된 Dockerfile
FROM python:3.12-slim

# UV 설치
COPY --from=ghcr.io/astral-sh/uv:latest /uv /usr/local/bin/uv

WORKDIR /app

# 의존성 파일만 먼저 복사 (캐시 최적화)
COPY pyproject.toml uv.lock ./

# Production 의존성만 설치
RUN uv sync --frozen --no-dev

# 애플리케이션 코드 복사
COPY . .

# UV로 실행
CMD ["uv", "run", "uvicorn", "main:app", "--host", "0.0.0.0"]
```

**장점:**
- 단일 스테이지로 간소화
- UV의 빠른 설치 속도 활용
- uv.lock으로 정확한 재현성 보장
- `--frozen` 플래그로 lock 파일 불일치 방지

## Monitoring & Alerts

### 1. GitHub Security Alerts
Enable Dependabot alerts:
- Settings → Security & analysis → Dependabot alerts

### 2. Snyk Integration

```yaml
# .github/workflows/snyk.yml
name: Snyk Security

on: [push, pull_request]

jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: snyk/actions/python@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        with:
          command: test
```

### 3. Regular Audits

```bash
# Weekly cron job
0 0 * * 0 cd /path/to/project && pip-audit | mail -s "Dependency Audit" team@example.com
```

## Quick Commands

```bash
# UV를 사용하는 경우 (권장)
# 전체 의존성 검토
uv tree
uv tree --outdated
uv add --dev pip-audit && pip-audit
uv add --dev safety && safety check
uv add --dev pip-licenses && pip-licenses

# 업데이트 워크플로우
uv lock --upgrade
uv sync
uv run pytest

# 보안 수정
uv add --dev pip-audit && pip-audit --fix
```

## Dependency Review Checklist

- [ ] All dependencies have known purpose
- [ ] No unused dependencies
- [ ] No known security vulnerabilities
- [ ] Licenses are compatible
- [ ] No dependency conflicts
- [ ] Versions are pinned in uv.lock (UV) or requirements.txt
- [ ] Regular update schedule established
- [ ] Automated scanning configured
- [ ] Development dependencies separated in pyproject.toml
- [ ] Documentation updated

## References
- [UV Documentation](https://github.com/astral-sh/uv)
- [Python Packaging Guide](https://packaging.python.org/)
- [PEP 621 - Dependency Specification](https://peps.python.org/pep-0621/)
- [PEP 735 - Dependency Groups](https://peps.python.org/pep-0735/)
- [OWASP Dependency Check](https://owasp.org/www-project-dependency-check/)
- [Snyk Python Security](https://snyk.io/python/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hakjunmin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

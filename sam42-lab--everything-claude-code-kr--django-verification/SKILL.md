---
name: django-verification
description: Django 프로젝트를 위한 검증 루프: 배포나 PR 전 마이그레이션, 린팅, 커버리지가 포함된 테스트, 보안 스캔 및 배포 준비 상태를 점검합니다. Use when this capability is needed.
metadata:
  author: SAM42-Lab
---

# Django 검증 루프 (Verification Loop)

PR 전, 주요 변경 후, 그리고 배포 전에 실행하여 Django 애플리케이션의 품질과 보안을 보장합니다.

## 활성화 시점

- Django 프로젝트에서 풀 리퀘스트(PR)를 보내기 전
- 주요 모델 변경, 마이그레이션 업데이트 또는 의존성 업그레이드 후
- 스테이징 또는 프로덕션 배포 전 검증 시
- 환경 확인 → 린트 → 테스트 → 보안 → 배포 준비 파이프라인 전체를 실행할 때
- 마이그레이션 안정성 및 테스트 커버리지를 검증할 때

## 1단계: 환경 확인

```bash
# Python 버전 확인
python --version  # 프로젝트 요구 사항과 일치해야 함

# 가상 환경 확인
which python
pip list --outdated

# 환경 변수 확인
python -c "import os; import environ; print('DJANGO_SECRET_KEY set' if os.environ.get('DJANGO_SECRET_KEY') else 'MISSING: DJANGO_SECRET_KEY')"
```

환경 설정이 잘못된 경우, 중단하고 수정하세요.

## 2단계: 코드 품질 및 포맷팅

```bash
# 타입 체크
mypy . --config-file pyproject.toml

# ruff를 이용한 린팅
ruff check . --fix

# black을 이용한 포맷팅 확인
black . --check
black .  # 자동 수정

# 임포트 정렬
isort . --check-only
isort .  # 자동 수정

# Django 전용 체크
python manage.py check --deploy
```

흔히 발생하는 문제:
- 공개 함수에 타입 힌트 누락
- PEP 8 포맷팅 위반
- 정렬되지 않은 임포트 구문
- 프로덕션 설정에 Debug 설정이 남은 경우

## 3단계: 마이그레이션

```bash
# 적용되지 않은 마이그레이션 확인
python manage.py showmigrations

# 누락된 마이그레이션 생성
python manage.py makemigrations --check

# 마이그레이션 적용 시뮬레이션
python manage.py migrate --plan

# 마이그레이션 적용 (테스트 환경)
python manage.py migrate

# 마이그레이션 충돌 확인
python manage.py makemigrations --merge  # 충돌이 있는 경우에만 실행
```

보고 항목:
- 대기 중인 마이그레이션 수
- 마이그레이션 충돌 여부
- 마이그레이션이 없는 모델 변경 사항

## 4단계: 테스트 + 커버리지

```bash
# pytest를 사용하여 모든 테스트 실행
pytest --cov=apps --cov-report=html --cov-report=term-missing --reuse-db

# 특정 앱의 테스트만 실행
pytest apps/users/tests/

# 마커를 이용한 실행
pytest -m "not slow"  # 느린 테스트 제외
pytest -m integration  # 통합 테스트만 실행

# 커버리지 리포트 확인
open htmlcov/index.html
```

보고 항목:
- 전체 테스트 결과: X 통과, Y 실패, Z 건너뜀
- 전체 커버리지: XX%
- 앱별 커버리지 상세 내역

커버리지 목표:

| 컴포넌트 | 목표 |
|-----------|--------|
| Models | 90%+ |
| Serializers | 85%+ |
| Views | 80%+ |
| Services | 90%+ |
| 전체 | 80%+ |

## 5단계: 보안 스캔

```bash
# 의존성 취약점 점검
pip-audit
safety check --full-report

# Django 보안 체크
python manage.py check --deploy

# Bandit 보안 린터
bandit -r . -f json -o bandit-report.json

# 시크릿 정보 유출 스캔 (gitleaks가 설치된 경우)
gitleaks detect --source . --verbose

# 환경 변수 확인
python -c "from django.core.exceptions import ImproperlyConfigured; from django.conf import settings; settings.DEBUG"
```

보고 항목:
- 취약한 의존성 발견 여부
- 보안 설정 문제
- 하드코딩된 시크릿 감지 여부
- DEBUG 모드 상태 (프로덕션에서는 False여야 함)

## 6단계: Django 관리 명령

```bash
# 모델 문제 확인
python manage.py check

# 정적 파일 수집
python manage.py collectstatic --noinput --clear

# 테스트를 위한 슈퍼유저 생성 (필요한 경우)
echo "from apps.users.models import User; User.objects.create_superuser('admin@example.com', 'admin')" | python manage.py shell

# 데이터베이스 무결성 확인
python manage.py check --database default

# 캐시 검증 (Redis 사용 시)
python -c "from django.core.cache import cache; cache.set('test', 'value', 10); print(cache.get('test'))"
```

## 7단계: 성능 체크

```bash
# Django Debug Toolbar 확인 (N+1 쿼리 점검)
# DEBUG=True 상태에서 페이지를 로드하고
# SQL 패널에서 중복 쿼리를 확인합니다.

# 쿼리 수 분석
django-admin debugsqlshell  # django-debug-sqlshell이 설치된 경우

# 누락된 인덱스 확인
python manage.py shell << EOF
from django.db import connection
with connection.cursor() as cursor:
    cursor.execute("SELECT table_name, index_name FROM information_schema.statistics WHERE table_schema = 'public'")
    print(cursor.fetchall())
EOF
```

보고 항목:
- 페이지당 쿼리 수 (일반적인 페이지의 경우 50개 미만 권장)
- 누락된 데이터베이스 인덱스
- 감지된 중복 쿼리

## 8단계: 정적 자산(Static Assets)

```bash
# npm 의존성 확인 (npm 사용 시)
npm audit
npm audit fix

# 정적 파일 빌드 (webpack/vite 사용 시)
npm run build

# 정적 파일 확인
ls -la staticfiles/
python manage.py findstatic css/style.css
```

## 9단계: 설정 리뷰

```python
# Python 쉘에서 설정 값 검증
python manage.py shell << EOF
from django.conf import settings
import os

# 주요 체크 항목
checks = {
    'DEBUG is False': not settings.DEBUG,
    'SECRET_KEY set': bool(settings.SECRET_KEY and len(settings.SECRET_KEY) > 30),
    'ALLOWED_HOSTS set': len(settings.ALLOWED_HOSTS) > 0,
    'HTTPS enabled': getattr(settings, 'SECURE_SSL_REDIRECT', False),
    'HSTS enabled': getattr(settings, 'SECURE_HSTS_SECONDS', 0) > 0,
    'Database configured': settings.DATABASES['default']['ENGINE'] != 'django.db.backends.sqlite3',
}

for check, result in checks.items():
    status = '✓' if result else '✗'
    print(f"{status} {check}")
EOF
```

## 10단계: 로깅 설정 확인

```bash
# 로깅 출력 테스트
python manage.py shell << EOF
import logging
logger = logging.getLogger('django')
logger.warning('Test warning message')
logger.error('Test error message')
EOF

# 로그 파일 확인 (설정된 경우)
tail -f /var/log/django/django.log
```

## 11단계: API 문서 확인 (DRF 사용 시)

```bash
# 스키마 생성
python manage.py generateschema --format openapi-json > schema.json

# 스키마 검증
# schema.json이 유효한 JSON인지 확인
python -c "import json; json.load(open('schema.json'))"

# Swagger UI 접속 (drf-yasg 사용 시)
# 브라우저에서 http://localhost:8000/swagger/ 접속
```

## 12단계: 변경 사항(Diff) 리뷰

```bash
# 변경 통계 확인
git diff --stat

# 실제 변경 내용 확인
git diff

# 변경된 파일 목록 확인
git diff --name-only

# 일반적인 문제 확인
git diff | grep -i "todo\|fixme\|hack\|xxx"
git diff | grep "print("  # 디버그 문구
git diff | grep "DEBUG = True"  # 디버그 모드
git diff | grep "import pdb"  # 디버거
```

체크리스트:
- 디버깅 문구 없음 (print, pdb, breakpoint())
- 중요 코드에 TODO/FIXME 주석 없음
- 하드코딩된 시크릿이나 자격 증명 없음
- 모델 변경에 따른 데이터베이스 마이그레이션 포함됨
- 설정 변경 사항이 문서화됨
- 외부 호출에 대한 에러 처리 구현됨
- 필요한 곳에 트랜잭션 관리 적용됨

## 리포트 템플릿 예시

```
DJANGO 검증 리포트
==========================

1단계: 환경 확인
  ✓ Python 3.11.5
  ✓ 가상 환경 활성화됨
  ✓ 모든 환경 변수 설정됨

2단계: 코드 품질
  ✓ mypy: 타입 에러 없음
  ✗ ruff: 3개 이슈 발견 (자동 수정됨)
  ✓ black: 포맷팅 이슈 없음
  ✓ isort: 임포트 정렬 완료
  ✓ manage.py check: 이슈 없음

3단계: 마이그레이션
  ✓ 적용되지 않은 마이그레이션 없음
  ✓ 마이그레이션 충돌 없음
  ✓ 모든 모델에 마이그레이션 존재함

4단계: 테스트 + 커버리지
  테스트: 247 통과, 0 실패, 5 건너뜀
  커버리지:
    전체: 87%
    users: 92%
    products: 89%
    orders: 85%
    payments: 91%

5단계: 보안 스캔
  ✗ pip-audit: 2개 취약점 발견 (수정 필요)
  ✓ safety check: 이슈 없음
  ✓ bandit: 보안 이슈 없음
  ✓ 시크릿 감지되지 않음
  ✓ DEBUG = False

6단계: Django 명령
  ✓ collectstatic 완료
  ✓ 데이터베이스 무결성 OK
  ✓ 캐시 백엔드 연결 가능

7단계: 성능
  ✓ N+1 쿼리 감지되지 않음
  ✓ 데이터베이스 인덱스 설정됨
  ✓ 쿼리 수 적정함

8단계: 정적 자산
  ✓ npm audit: 취약점 없음
  ✓ 자산 빌드 성공
  ✓ 정적 파일 수집 완료

9단계: 설정
  ✓ DEBUG = False
  ✓ SECRET_KEY 설정됨
  ✓ ALLOWED_HOSTS 설정됨
  ✓ HTTPS 활성화됨
  ✓ HSTS 활성화됨
  ✓ 데이터베이스 설정됨

10단계: 로깅
  ✓ 로깅 설정됨
  ✓ 로그 파일 쓰기 가능

11단계: API 문서
  ✓ 스키마 생성됨
  ✓ Swagger UI 접근 가능

12단계: 변경 사항 리뷰
  변경된 파일: 12개
  +450, -120 라인
  ✓ 디버그 문구 없음
  ✓ 하드코딩된 시크릿 없음
  ✓ 마이그레이션 포함됨

권장 사항: 경고: 배포 전 pip-audit 취약점을 수정하세요.

다음 단계:
1. 취약한 의존성 업데이트
2. 보안 스캔 재실행
3. 최종 테스트를 위해 스테이징 환경에 배포
```

## 배포 전 체크리스트

- [ ] 모든 테스트 통과
- [ ] 커버리지 ≥ 80%
- [ ] 보안 취약점 없음
- [ ] 적용되지 않은 마이그레이션 없음
- [ ] 프로덕션 설정에서 DEBUG = False 확인
- [ ] SECRET_KEY가 적절히 구성됨
- [ ] ALLOWED_HOSTS가 올바르게 설정됨
- [ ] 데이터베이스 백업 활성화됨
- [ ] 정적 파일이 수집되고 서비스됨
- [ ] 로깅이 구성되고 작동함
- [ ] 에러 모니터링(Sentry 등)이 구성됨
- [ ] CDN 설정됨 (해당하는 경우)
- [ ] Redis/캐시 백엔드 설정됨
- [ ] Celery 워커 실행 중 (해당하는 경우)
- [ ] HTTPS/SSL 설정됨
- [ ] 환경 변수가 문서화됨

## 지속적 통합 (CI)

### GitHub Actions 예시

```yaml
# .github/workflows/django-verification.yml
name: Django Verification

on: [push, pull_request]

jobs:
  verify:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:14
        env:
          POSTGRES_PASSWORD: postgres
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

    steps:
      - uses: actions/checkout@v3

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'

      - name: Cache pip
        uses: actions/cache@v3
        with:
          path: ~/.cache/pip
          key: ${{ runner.os }}-pip-${{ hashFiles('**/requirements.txt') }}

      - name: Install dependencies
        run: |
          pip install -r requirements.txt
          pip install ruff black mypy pytest pytest-django pytest-cov bandit safety pip-audit

      - name: Code quality checks
        run: |
          ruff check .
          black . --check
          isort . --check-only
          mypy .

      - name: Security scan
        run: |
          bandit -r . -f json -o bandit-report.json
          safety check --full-report
          pip-audit

      - name: Run tests
        env:
          DATABASE_URL: postgres://postgres:postgres@localhost:5432/test
          DJANGO_SECRET_KEY: test-secret-key
        run: |
          pytest --cov=apps --cov-report=xml --cov-report=term-missing

      - name: Upload coverage
        uses: codecov/codecov-action@v3
```

## 빠른 참조

| 확인 사항 | 명령어 |
|-------|---------|
| 환경 | `python --version` |
| 타입 체크 | `mypy .` |
| 린팅 | `ruff check .` |
| 포맷팅 | `black . --check` |
| 마이그레이션 | `python manage.py makemigrations --check` |
| 테스트 | `pytest --cov=apps` |
| 보안 | `pip-audit && bandit -r .` |
| Django 체크 | `python manage.py check --deploy` |
| 정적 파일 수집 | `python manage.py collectstatic --noinput` |
| 변경 통계 | `git diff --stat` |

기억하세요: 자동화된 검증은 일반적인 문제를 잡아내지만, 수동 코드 리뷰와 스테이징 환경에서의 테스트를 대신할 수는 없습니다.

---
> Source: [SAM42-Lab/everything-claude-code-kr](https://github.com/SAM42-Lab/everything-claude-code-kr) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->

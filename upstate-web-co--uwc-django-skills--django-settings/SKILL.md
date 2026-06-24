---
name: django-settings
description: Apply this skill when managing Django settings across environments. Covers split settings pattern (base.py + development.py + production.py), environment variable management, secret handling, and the anti-pattern of hardcoding values. Triggered by phrases like 'Django settings', 'settings.py', 'environment variables', 'DJANGO_SECRET_KEY', or when configuring Django for deployment. Use when this capability is needed.
metadata:
  author: upstate-web-co
---

## Trigger
You need to set up Django settings for a project that will run in development, testing/CI, and production environments.

## Summary (Human)
Split settings into a module with a shared `base.py` and per-environment overrides. CI/testing settings optimize for speed (MD5 hasher, eager Celery, no throttling, SQLite locally). Production settings enforce security (HSTS, secure cookies, Sentry). Environment selection via `DJANGO_SETTINGS_MODULE` env var or auto-detection in `__init__.py`.

## Procedure (Claude)

### 1. Directory Structure

```
config/
  settings/
    __init__.py        ← Environment selector
    base.py            ← Shared config (all environments)
    development.py     ← Local dev (SQLite, console email, eager Celery)
    testing.py         ← Tests + CI (MD5 hasher, no throttling)
    production.py      ← Production (Postgres, Sentry, security)
```

### 2. Environment Selection

**Option A: Direct `DJANGO_SETTINGS_MODULE`** (Shira pattern — simpler):
```python
# __init__.py — empty file
# Set DJANGO_SETTINGS_MODULE in environment:
#   dev:  config.settings.development
#   CI:   config.settings.testing
#   prod: config.settings.production
```

**Option B: Auto-detection in `__init__.py`** (MyChama pattern — more convenient):
```python
# __init__.py
from decouple import config

environment = config('DJANGO_ENVIRONMENT', default='development')

if environment == 'production':
    from .production import *
elif environment == 'staging':
    from .staging import *
elif environment == 'ci':
    from .ci import *
else:
    from .development import *
```

Option A is cleaner (explicit, no import magic). Option B is more convenient for developers who don't want to set `DJANGO_SETTINGS_MODULE`.

### 3. base.py (Shared Config)

```python
# config/settings/base.py
import os
from pathlib import Path
from datetime import timedelta

BASE_DIR = Path(__file__).resolve().parent.parent.parent

# SECRET_KEY: safe default for dev, crash in prod if missing
_default_key = 'django-insecure-CHANGE-ME-IN-PRODUCTION'
SECRET_KEY = os.environ.get('DJANGO_SECRET_KEY', _default_key)
if (SECRET_KEY == _default_key
    and os.environ.get('DJANGO_ENVIRONMENT') == 'production'):
    raise ValueError('DJANGO_SECRET_KEY must be set in production.')

DEBUG = False  # override in dev

# App categories
DJANGO_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
THIRD_PARTY_APPS = [
    'rest_framework',
    'rest_framework_simplejwt',
    'rest_framework_simplejwt.token_blacklist',
    'django_filters',
    'corsheaders',
    'django_celery_beat',
]
LOCAL_APPS = [
    'apps.core',
    # ... project-specific apps
]
INSTALLED_APPS = DJANGO_APPS + THIRD_PARTY_APPS + LOCAL_APPS

# Custom user model
AUTH_USER_MODEL = 'core.AppUser'

# Middleware (SecurityHeaders FIRST, Audit LAST)
MIDDLEWARE = [
    'apps.core.middleware.security.SecurityHeadersMiddleware',
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'apps.core.middleware.audit.InputSanitizationMiddleware',
    'apps.core.middleware.tenant.TenantMiddleware',
    'apps.core.middleware.audit.SecurityAuditMiddleware',
]

# DRF
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ],
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 50,
    'DEFAULT_FILTER_BACKENDS': [
        'django_filters.rest_framework.DjangoFilterBackend',
        'rest_framework.filters.SearchFilter',
        'rest_framework.filters.OrderingFilter',
    ],
    'DEFAULT_THROTTLE_CLASSES': [
        'apps.core.permissions.RoleBasedRateThrottle',
    ],
    'DEFAULT_THROTTLE_RATES': {
        'role_owner': '300/hour',
        'role_manager': '300/hour',
        'anon': '30/hour',
        'pin_login': '5/hour',
    },
    'EXCEPTION_HANDLER': 'config.exception_handler.custom_exception_handler',
}

# JWT
SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=30),
    'REFRESH_TOKEN_LIFETIME': timedelta(days=1),
    'ROTATE_REFRESH_TOKENS': True,
    'BLACKLIST_AFTER_ROTATION': True,
}

# Celery
CELERY_BROKER_URL = os.environ.get('REDIS_URL', 'redis://localhost:6379/0')
CELERY_RESULT_BACKEND = CELERY_BROKER_URL
CELERY_TIMEZONE = 'Africa/Nairobi'  # or project-appropriate
CELERY_TASK_ROUTES = {
    'apps.analytics.tasks.*': {'queue': 'analytics'},
    'apps.notifications.tasks.*': {'queue': 'notifications'},
}

# Static files
STATIC_URL = '/static/'
STATIC_ROOT = BASE_DIR / 'staticfiles'
```

### 4. development.py

```python
from .base import *

DEBUG = True
ALLOWED_HOSTS = ['*']

# SQLite for simplicity
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}

# Console email
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'

# Local memory cache
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
    }
}

# Synchronous Celery (no Redis needed)
CELERY_TASK_ALWAYS_EAGER = True
CELERY_TASK_EAGER_PROPAGATES = True

# Faster password hashing for dev
PASSWORD_HASHERS = ['django.contrib.auth.hashers.MD5PasswordHasher']

# No throttling
REST_FRAMEWORK['DEFAULT_THROTTLE_CLASSES'] = []

# CORS allow all in dev
CORS_ALLOW_ALL_ORIGINS = True
```

### 5. testing.py

```python
from .base import *

DEBUG = False  # match production behavior

# SQLite locally, Postgres in CI (via DATABASE_URL)
import dj_database_url
DATABASES = {
    'default': dj_database_url.config(
        default='sqlite:///test.sqlite3'
    )
}

# 192x faster password hashing
PASSWORD_HASHERS = ['django.contrib.auth.hashers.MD5PasswordHasher']

# Synchronous Celery
CELERY_TASK_ALWAYS_EAGER = True
CELERY_TASK_EAGER_PROPAGATES = True

# In-memory email
EMAIL_BACKEND = 'django.core.mail.backends.locmem.EmailBackend'

# No throttling (prevents flaky tests)
REST_FRAMEWORK['DEFAULT_THROTTLE_CLASSES'] = []

# Suppress logging noise
LOGGING = {
    'version': 1,
    'disable_existing_loggers': True,
    'handlers': {},
    'root': {'handlers': [], 'level': 'CRITICAL'},
}

# Local memory cache
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
    }
}
```

### 6. production.py

```python
from .base import *

DEBUG = False

# MUST be set in environment
SECRET_KEY = os.environ['DJANGO_SECRET_KEY']

ALLOWED_HOSTS = os.environ.get('ALLOWED_HOSTS', '').split(',')

# PostgreSQL with connection pooling
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.environ.get('POSTGRES_DB'),
        'USER': os.environ.get('POSTGRES_USER'),
        'PASSWORD': os.environ.get('POSTGRES_PASSWORD'),
        'HOST': os.environ.get('POSTGRES_HOST', 'db'),
        'PORT': os.environ.get('POSTGRES_PORT', '5432'),
        'CONN_MAX_AGE': 600,
    }
}

# Redis cache with connection pooling
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.redis.RedisCache',
        'LOCATION': os.environ.get('REDIS_URL', 'redis://redis:6379/1'),
    }
}

# SMTP email
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = os.environ.get('EMAIL_HOST', 'smtp.gmail.com')
EMAIL_PORT = int(os.environ.get('EMAIL_PORT', '587'))
EMAIL_USE_TLS = True
EMAIL_HOST_USER = os.environ.get('EMAIL_HOST_USER', '')
EMAIL_HOST_PASSWORD = os.environ.get('EMAIL_HOST_PASSWORD', '')

# Security
SECURE_SSL_REDIRECT = False  # handled by nginx/Cloudflare
SECURE_HSTS_SECONDS = 31536000
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')

# Sentry (optional)
SENTRY_DSN = os.environ.get('SENTRY_DSN')
if SENTRY_DSN:
    import sentry_sdk
    sentry_sdk.init(
        dsn=SENTRY_DSN,
        traces_sample_rate=0.1,
        send_default_pii=False,
    )
```

### 7. Requirements Split

```
requirements/
  base.txt     ← Production deps
  dev.txt      ← -r base.txt + ruff, ipdb, debug-toolbar
  ci.txt       ← -r base.txt + pytest, coverage, factory-boy
```

## Anti-Patterns

| Anti-Pattern | Correct |
|---|---|
| Single `settings.py` with `if DEBUG` checks | Split into per-environment files |
| `SECRET_KEY` with fallback in production | Crash on startup if missing |
| `DEBUG = True` in base.py | `DEBUG = False` in base, override only in dev |
| MD5 hasher in production | Only in dev/testing (192x speedup) |
| Skip `CELERY_TASK_ALWAYS_EAGER` in testing | Tests must not depend on Redis/broker |
| Throttling enabled in tests | Disable to prevent flaky test failures |
| `CONN_MAX_AGE = 0` in production | Set to 600 for connection reuse |
| SQLite in CI with Postgres in prod | CI uses `DATABASE_URL` for Postgres parity |
| `SECURE_SSL_REDIRECT = True` behind nginx | Set `False`, let nginx/CF handle SSL redirect |
| `send_default_pii = True` in Sentry | Always `False` |

## Source
- Shira: `config/settings/` (4-tier: base, development, testing, production)
- MyChama: `chamagroup/settings/` (5-tier: base, development, ci, staging, production)

---
> Source: [upstate-web-co/uwc-django-skills](https://github.com/upstate-web-co/uwc-django-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->

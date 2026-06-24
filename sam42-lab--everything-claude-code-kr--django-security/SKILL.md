---
name: django-security
description: Django 보안 모범 사례, 인증, 인가, CSRF 보호, SQL 인젝션 방지, XSS 방지 및 보안 배포 설정에 관한 가이드입니다. Use when this capability is needed.
metadata:
  author: SAM42-Lab
---

# Django 보안 모범 사례

일반적인 취약점으로부터 Django 애플리케이션을 보호하기 위한 포괄적인 보안 가이드라인입니다.

## 활성화 시점

- Django 인증 및 인가 시스템을 설정할 때
- 사용자 권한 및 역할을 구현할 때
- 프로덕션 환경의 보안 설정을 구성할 때
- 보안 문제에 대해 Django 애플리케이션을 검토할 때
- Django 애플리케이션을 프로덕션에 배포할 때

## 핵심 보안 설정

### 프로덕션 환경 설정 구성

```python
# settings/production.py
import os

DEBUG = False  # 중요: 프로덕션에서는 절대 True를 사용하지 마세요.

ALLOWED_HOSTS = os.environ.get('ALLOWED_HOSTS', '').split(',')

# 보안 헤더 설정
SECURE_SSL_REDIRECT = True
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
SECURE_HSTS_SECONDS = 31536000  # 1년
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True
SECURE_CONTENT_TYPE_NOSNIFF = True
SECURE_BROWSER_XSS_FILTER = True
X_FRAME_OPTIONS = 'DENY'

# HTTPS 및 쿠키 설정
SESSION_COOKIE_HTTPONLY = True
CSRF_COOKIE_HTTPONLY = True
SESSION_COOKIE_SAMESITE = 'Lax'
CSRF_COOKIE_SAMESITE = 'Lax'

# Secret key (반드시 환경 변수를 통해 설정해야 함)
SECRET_KEY = os.environ.get('DJANGO_SECRET_KEY')
if not SECRET_KEY:
    raise ImproperlyConfigured('DJANGO_SECRET_KEY environment variable is required')

# 비밀번호 유효성 검사
AUTH_PASSWORD_VALIDATORS = [
    {
        'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
        'OPTIONS': {
            'min_length': 12,
        }
    },
    {
        'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
    },
]
```

## 인증 (Authentication)

### 커스텀 사용자 모델

```python
# apps/users/models.py
from django.contrib.auth.models import AbstractUser
from django.db import models

class User(AbstractUser):
    """보안 강화를 위한 커스텀 사용자 모델."""

    email = models.EmailField(unique=True)
    phone = models.CharField(max_length=20, blank=True)

    USERNAME_FIELD = 'email'  # 이메일을 사용자 아이디로 사용
    REQUIRED_FIELDS = ['username']

    class Meta:
        db_table = 'users'
        verbose_name = 'User'
        verbose_name_plural = 'Users'

    def __str__(self):
        return self.email

# settings/base.py
AUTH_USER_MODEL = 'users.User'
```

### 비밀번호 해싱

```python
# Django는 기본적으로 PBKDF2를 사용합니다. 보안 강화를 위해 Argon2를 권장합니다.
PASSWORD_HASHERS = [
    'django.contrib.auth.hashers.Argon2PasswordHasher',
    'django.contrib.auth.hashers.PBKDF2PasswordHasher',
    'django.contrib.auth.hashers.PBKDF2SHA1PasswordHasher',
    'django.contrib.auth.hashers.BCryptSHA256PasswordHasher',
]
```

### 세션 관리

```python
# 세션 구성
SESSION_ENGINE = 'django.contrib.sessions.backends.cache'  # 또는 'db'
SESSION_CACHE_ALIAS = 'default'
SESSION_COOKIE_AGE = 3600 * 24 * 7  # 1주일
SESSION_SAVE_EVERY_REQUEST = False
SESSION_EXPIRE_AT_BROWSER_CLOSE = False  # 사용자 경험(UX)에는 좋지만 보안성은 낮음
```

## 인가 (Authorization)

### 권한 관리

```python
# models.py
from django.db import models
from django.contrib.auth.models import Permission

class Post(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    author = models.ForeignKey(User, on_delete=models.CASCADE)

    class Meta:
        permissions = [
            ('can_publish', 'Can publish posts'),
            ('can_edit_others', 'Can edit posts of others'),
        ]

    def user_can_edit(self, user):
        """사용자가 이 포스트를 수정할 수 있는지 확인."""
        return self.author == user or user.has_perm('app.can_edit_others')

# views.py
from django.contrib.auth.mixins import LoginRequiredMixin, PermissionRequiredMixin
from django.views.generic import UpdateView

class PostUpdateView(LoginRequiredMixin, PermissionRequiredMixin, UpdateView):
    model = Post
    permission_required = 'app.can_edit_others'
    raise_exception = True  # 리다이렉트 대신 403 반환

    def get_queryset(self):
        """사용자가 자신의 포스트만 수정할 수 있도록 허용."""
        return Post.objects.filter(author=self.request.user)
```

### 커스텀 권한 (DRF)

```python
# permissions.py
from rest_framework import permissions

class IsOwnerOrReadOnly(permissions.BasePermission):
    """소유자만 객체를 수정할 수 있도록 허용."""

    def has_object_permission(self, request, view, obj):
        # 읽기 권한은 모든 요청에 허용
        if request.method in permissions.SAFE_METHODS:
            return True

        # 쓰기 권한은 소유자에게만 허용
        return obj.author == request.user

class IsAdminOrReadOnly(permissions.BasePermission):
    """관리자에게는 모든 권한을, 나머지에겐 읽기 전용 권한 부여."""

    def has_permission(self, request, view):
        if request.method in permissions.SAFE_METHODS:
            return True
        return request.user and request.user.is_staff

class IsVerifiedUser(permissions.BasePermission):
    """인증된 사용자만 허용."""

    def has_permission(self, request, view):
        return request.user and request.user.is_authenticated and request.user.is_verified
```

### 역할 기반 액세스 제어 (RBAC)

```python
# models.py
from django.contrib.auth.models import AbstractUser, Group

class User(AbstractUser):
    ROLE_CHOICES = [
        ('admin', 'Administrator'),
        ('moderator', 'Moderator'),
        ('user', 'Regular User'),
    ]
    role = models.CharField(max_length=20, choices=ROLE_CHOICES, default='user')

    def is_admin(self):
        return self.role == 'admin' or self.is_superuser

    def is_moderator(self):
        return self.role in ['admin', 'moderator']

# Mixins
class AdminRequiredMixin:
    """관리자 역할이 필요한 Mixin."""

    def dispatch(self, request, *args, **kwargs):
        if not request.user.is_authenticated or not request.user.is_admin():
            from django.core.exceptions import PermissionDenied
            raise PermissionDenied
        return super().dispatch(request, *args, **kwargs)
```

## SQL 인젝션 방지

### Django ORM 보호

```python
# 좋음: Django ORM은 파라미터를 자동으로 이스케이프함
def get_user(username):
    return User.objects.get(username=username)  # 안전

# 좋음: raw() 사용 시 파라미터 전달 방식 사용
def search_users(query):
    return User.objects.raw('SELECT * FROM users WHERE username = %s', [query])

# 나쁨: 사용자 입력을 직접 문자열에 삽입하지 마세요.
def get_user_bad(username):
    return User.objects.raw(f'SELECT * FROM users WHERE username = {username}')  # 취약함!

# 좋음: 적절한 이스케이프가 포함된 filter 사용
def get_users_by_email(email):
    return User.objects.filter(email__iexact=email)  # 안전

# 좋음: 복잡한 쿼리에 Q 객체 사용
from django.db.models import Q
def search_users_complex(query):
    return User.objects.filter(
        Q(username__icontains=query) |
        Q(email__icontains=query)
    )  # 안전
```

### raw() 사용 시 추가 보안

```python
# 원시 SQL을 사용해야 할 경우 항상 파라미터 방식을 사용하세요.
User.objects.raw(
    'SELECT * FROM users WHERE email = %s AND status = %s',
    [user_input_email, status]
)
```

## XSS (교차 사이트 스크립팅) 방지

### 템플릿 이스케이핑

```django
{# Django는 기본적으로 변수를 자동 이스케이프함 - 안전 #}
{{ user_input }}  {# HTML이 이스케이프됨 #}

{# 신뢰할 수 있는 콘텐츠에만 명시적으로 safe 표시 사용 #}
{{ trusted_html|safe }}  {# 이스케이프되지 않음 #}

{# 안전한 HTML을 위해 템플릿 필터 사용 #}
{{ user_input|escape }}  {# 기본값과 동일 #}
{{ user_input|striptags }}  {# 모든 HTML 태그 제거 #}

{# JavaScript 이스케이핑 #}
<script>
    var username = {{ username|escapejs }};
</script>
```

### 안전한 문자열 처리

```python
from django.utils.safestring import mark_safe
from django.utils.html import escape

# 나쁨: 이스케이프 없이 사용자 입력을 safe로 표시하지 마세요.
def render_bad(user_input):
    return mark_safe(user_input)  # 취약함!

# 좋음: 먼저 이스케이프한 후 safe로 표시
def render_good(user_input):
    return mark_safe(escape(user_input))

# 좋음: 변수가 포함된 HTML에는 format_html 사용
from django.utils.html import format_html

def greet_user(username):
    return format_html('<span class="user">{}</span>', escape(username))
```

### HTTP 헤더

```python
# settings.py
SECURE_CONTENT_TYPE_NOSNIFF = True  # MIME 스니핑 방지
SECURE_BROWSER_XSS_FILTER = True  # 브라우저 XSS 필터 활성화
X_FRAME_OPTIONS = 'DENY'  # 클릭재킹 방지

# 커스텀 미들웨어
from django.conf import settings

class SecurityHeaderMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        response = self.get_response(request)
        response['X-Content-Type-Options'] = 'nosniff'
        response['X-Frame-Options'] = 'DENY'
        response['X-XSS-Protection'] = '1; mode=block'
        response['Content-Security-Policy'] = "default-src 'self'"
        return response
```

## CSRF 보호

### 기본 CSRF 보호

```python
# settings.py - CSRF는 기본적으로 활성화되어 있음
CSRF_COOKIE_SECURE = True  # HTTPS를 통해서만 전송
CSRF_COOKIE_HTTPONLY = True  # JavaScript 접근 방지
CSRF_COOKIE_SAMESITE = 'Lax'  # 일부 케이스에서 CSRF 방지
CSRF_TRUSTED_ORIGINS = ['https://example.com']  # 신뢰할 수 있는 도메인

# 템플릿 사용법
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Submit</button>
</form>

# AJAX 요청 처리
function getCookie(name) {
    let cookieValue = null;
    if (document.cookie && document.cookie !== '') {
        const cookies = document.cookie.split(';');
        for (let i = 0; i < cookies.length; i++) {
            const cookie = cookies[i].trim();
            if (cookie.substring(0, name.length + 1) === (name + '=')) {
                cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
                break;
            }
        }
    }
    return cookieValue;
}

fetch('/api/endpoint/', {
    method: 'POST',
    headers: {
        'X-CSRFToken': getCookie('csrftoken'),
        'Content-Type': 'application/json',
    },
    body: JSON.stringify(data)
});
```

### 뷰 제외 설정 (주의해서 사용)

```python
from django.views.decorators.csrf import csrf_exempt

@csrf_exempt  # 꼭 필요한 경우에만 사용하세요!
def webhook_view(request):
    # 외부 서비스로부터의 웹훅 처리
    pass
```

## 파일 업로드 보안

### 파일 유효성 검사

```python
import os
from django.core.exceptions import ValidationError

def validate_file_extension(value):
    """파일 확장자 검사."""
    ext = os.path.splitext(value.name)[1]
    valid_extensions = ['.jpg', '.jpeg', '.png', '.gif', '.pdf']
    if not ext.lower() in valid_extensions:
        raise ValidationError('Unsupported file extension.')

def validate_file_size(value):
    """파일 크기 검사 (최대 5MB)."""
    filesize = value.size
    if filesize > 5 * 1024 * 1024:
        raise ValidationError('File too large. Max size is 5MB.')

# models.py
class Document(models.Model):
    file = models.FileField(
        upload_to='documents/',
        validators=[validate_file_extension, validate_file_size]
    )
```

### 안전한 파일 저장

```python
# settings.py
MEDIA_ROOT = '/var/www/media/'
MEDIA_URL = '/media/'

# 프로덕션에서는 미디어용 별도 도메인 사용 권장
MEDIA_DOMAIN = 'https://media.example.com'

# 사용자 업로드 파일을 직접 서빙하지 마세요.
# 정적 파일에는 whitenoise나 CDN을 사용하세요.
# 미디어 파일에는 별도의 서버나 S3를 사용하세요.
```

## API 보안

### 속도 제한 (Rate Limiting)

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle'
    ],
    'DEFAULT_THROTTLE_RATES': {
        'anon': '100/day',
        'user': '1000/day',
        'upload': '10/hour',
    }
}

# 커스텀 Throttle
from rest_framework.throttling import UserRateThrottle

class BurstRateThrottle(UserRateThrottle):
    scope = 'burst'
    rate = '60/min'

class SustainedRateThrottle(UserRateThrottle):
    scope = 'sustained'
    rate = '1000/day'
```

### API 인증

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.TokenAuthentication',
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ],
}

# views.py
from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import IsAuthenticated

@api_view(['GET', 'POST'])
@permission_classes([IsAuthenticated])
def protected_view(request):
    return Response({'message': 'You are authenticated'})
```

## 보안 헤더

### 콘텐츠 보안 정책 (CSP)

```python
# settings.py
CSP_DEFAULT_SRC = "'self'"
CSP_SCRIPT_SRC = "'self' https://cdn.example.com"
CSP_STYLE_SRC = "'self' 'unsafe-inline'"
CSP_IMG_SRC = "'self' data: https:"
CSP_CONNECT_SRC = "'self' https://api.example.com"

# 미들웨어 예시
class CSPMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        response = self.get_response(request)
        response['Content-Security-Policy'] = (
            f"default-src {CSP_DEFAULT_SRC}; "
            f"script-src {CSP_SCRIPT_SRC}; "
            f"style-src {CSP_STYLE_SRC}; "
            f"img-src {CSP_IMG_SRC}; "
            f"connect-src {CSP_CONNECT_SRC}"
        )
        return response
```

## 환경 변수 관리

### 시크릿 정보 관리

```python
# python-decouple 또는 django-environ 사용 권장
import environ

env = environ.Env(
    # 타입 및 기본값 설정
    DEBUG=(bool, False)
)

# .env 파일 읽기
environ.Env.read_env()

SECRET_KEY = env('DJANGO_SECRET_KEY')
DATABASE_URL = env('DATABASE_URL')
ALLOWED_HOSTS = env.list('ALLOWED_HOSTS')

# .env 파일 예시 (절대 git에 커밋하지 마세요)
DEBUG=False
SECRET_KEY=your-secret-key-here
DATABASE_URL=postgresql://user:password@localhost:5432/dbname
ALLOWED_HOSTS=example.com,www.example.com
```

## 보안 이벤트 로깅

```python
# settings.py
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'file': {
            'level': 'WARNING',
            'class': 'logging.FileHandler',
            'filename': '/var/log/django/security.log',
        },
        'console': {
            'level': 'INFO',
            'class': 'logging.StreamHandler',
        },
    },
    'loggers': {
        'django.security': {
            'handlers': ['file', 'console'],
            'level': 'WARNING',
            'propagate': True,
        },
        'django.request': {
            'handlers': ['file'],
            'level': 'ERROR',
            'propagate': False,
        },
    },
}
```

## 빠른 보안 체크리스트

| 항목 | 설명 |
|-------|-------------|
| `DEBUG = False` | 프로덕션 환경에서 절대 DEBUG 모드로 실행하지 마세요. |
| HTTPS 전용 | SSL 강제 적용 및 보안 쿠키 설정 |
| 강력한 시크릿 정보 | SECRET_KEY 등에 환경 변수 사용 |
| 비밀번호 유효성 검사 | 모든 비밀번호 유효성 검사기 활성화 |
| CSRF 보호 | 기본적으로 활성화되어 있으며, 비활성화하지 마세요. |
| XSS 방지 | Django 자동 이스케이프 활용, 사용자 입력에 `|safe` 남용 금지 |
| SQL 인젝션 방지 | ORM 사용, 쿼리에 문자열 직접 결합 금지 |
| 파일 업로드 | 파일 타입 및 크기 유효성 검사 |
| 속도 제한 | API 엔드포인트에 Throttling 적용 |
| 보안 헤더 | CSP, X-Frame-Options, HSTS 등 설정 |
| 로깅 | 보안 이벤트 로깅 설정 |
| 업데이트 | Django 및 패키지를 항상 최신 버전으로 유지 |

기억하세요: 보안은 결과물이 아니라 지속적인 과정입니다. 정기적으로 보안 관행을 검토하고 업데이트하세요.

---
> Source: [SAM42-Lab/everything-claude-code-kr](https://github.com/SAM42-Lab/everything-claude-code-kr) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->

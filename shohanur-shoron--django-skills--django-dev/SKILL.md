---
name: django-dev
description: > Use when this capability is needed.
metadata:
  author: shohanur-shoron
---

# Django Development Skill

This skill covers professional Django development across two major paradigms:

1. **MVT** — Django's built-in template engine, HTML + Tailwind CSS + vanilla JS
2. **DRF** — Django REST Framework for building APIs

Read the appropriate reference file based on the task:
- MVT-focused tasks → `references/mvt.md`
- API / DRF-focused tasks → `references/drf.md`
- Mixed or architectural tasks → read both

---

## Universal Django Principles

### Modern Tooling & Workflow

For a professional setup, always use:
- **Linter/Formatter:** [Ruff](https://github.com/astral-sh/ruff) (replaces Flake8, Black, isort).
- **Package Management:** [uv](https://github.com/astral-sh/uv) or [Poetry](https://python-poetry.org/).
- **Dev Tools:** `django-debug-toolbar` (SQL inspection) and `django-extensions` (for `shell_plus`).

### Project Layout

Always follow this structure for new projects:

```
project_name/
├── manage.py
├── pyproject.toml           # Modern dependency management
├── config/                  # project-level settings & URLs
│   ├── settings/
│   │   ├── base.py
│   │   ├── dev.py
│   │   └── prod.py
│   ├── urls.py
│   └── wsgi.py / asgi.py
├── apps/
│   └── <app_name>/
│       ├── models.py
│       ├── views.py
│       ├── urls.py
│       ├── admin.py
│       ├── serializers.py   (DRF only)
│       ├── forms.py         (MVT only)
│       ├── templates/       (MVT only)
│       │   └── <app_name>/
│       ├── static/          (MVT only)
│       └── tests/
│           ├── test_models.py
│           ├── test_views.py
│           └── test_serializers.py
├── requirements/            # Optional if using uv/Poetry
│   ├── base.txt
│   ├── dev.txt
│   └── prod.txt
└── .env
```

### Models: Best Practices

```python
from django.db import models
from django.utils.translation import gettext_lazy as _
from django.urls import reverse

class Article(models.Model):
    class Status(models.TextChoices):
        DRAFT     = 'draft',     _('Draft')
        PUBLISHED = 'published', _('Published')

    title: str      = models.CharField(max_length=200)
    slug: str       = models.SlugField(unique=True)
    body: str       = models.TextField()
    status: str     = models.CharField(max_length=20, choices=Status.choices, default=Status.DRAFT)
    author          = models.ForeignKey('auth.User', on_delete=models.CASCADE, related_name='articles')
    created_at      = models.DateTimeField(auto_now_add=True)
    updated_at      = models.DateTimeField(auto_now=True)

    class Meta:
        ordering = ['-created_at']
        indexes  = [models.Index(fields=['slug'])]

    def __str__(self) -> str:
        return self.title

    def get_absolute_url(self) -> str:
        return reverse('blog:detail', kwargs={'slug': self.slug})
```

Key rules:
- Always add `__str__` and `get_absolute_url`.
- Use type hints for model fields where appropriate.
- Use `TextChoices` / `IntegerChoices` instead of raw string tuples.
- Add `related_name` to every ForeignKey.
- Index fields you'll filter or order by frequently.
- **Security:** Run `python manage.py check --deploy` before any production release.

### Custom User Model

**Always** set up a custom user model before the first migration. Never build on the default `User` if there's any chance you'll need to customize it later.

```python
# apps/accounts/models.py
from django.contrib.auth.models import AbstractUser

class User(AbstractUser):
    bio = models.TextField(blank=True)
    avatar = models.ImageField(upload_to='avatars/', null=True, blank=True)

# config/settings/base.py
AUTH_USER_MODEL = 'accounts.User'
```

### URLs

Use `app_name` in every `urls.py` for namespace support:

```python
# apps/blog/urls.py
app_name = 'blog'
urlpatterns = [
    path('', views.ArticleListView.as_view(), name='list'),
    path('<slug:slug>/', views.ArticleDetailView.as_view(), name='detail'),
]

# In templates: {% url 'blog:detail' article.slug %}
# In code: reverse('blog:detail', kwargs={'slug': article.slug})
```

### Django Admin

Register models with a proper `ModelAdmin` — never just `admin.site.register(Model)` alone:

```python
from django.contrib import admin
from .models import Article

@admin.register(Article)
class ArticleAdmin(admin.ModelAdmin[Article]):
    list_display: list[str]  = ['title', 'status', 'author', 'created_at']
    list_filter: list[str]   = ['status', 'author']
    search_fields: list[str] = ['title', 'body']
    prepopulated_fields      = {'slug': ('title',)}
    raw_id_fields: list[str] = ['author']
    date_hierarchy: str      = 'created_at'
```

### Environment Variables

Always use `python-decouple` or `django-environ`. Never hardcode secrets:

```python
# base.py
from decouple import config

SECRET_KEY = config('SECRET_KEY')
DEBUG       = config('DEBUG', default=False, cast=bool)
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': config('DB_NAME'),
        'USER': config('DB_USER'),
        'PASSWORD': config('DB_PASSWORD'),
        'HOST': config('DB_HOST', default='localhost'),
        'PORT': config('DB_PORT', default='5432'),
    }
}
```

---

## Reference Files

| Task type | File to read |
|---|---|
| Templates, forms, class-based views, Tailwind, JS | `references/mvt.md` |
| Serializers, ViewSets, permissions, JWT, filtering | `references/drf.md` |

Load both for tasks that combine API endpoints + rendered pages (common in hybrid apps).

---
> Source: [shohanur-shoron/Django-Skills](https://github.com/shohanur-shoron/Django-Skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->

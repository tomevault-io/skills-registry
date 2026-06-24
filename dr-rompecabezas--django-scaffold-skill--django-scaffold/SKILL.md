---
name: django-scaffold
description: Scaffold a new Django project following Felipe's established patterns Use when this capability is needed.
metadata:
  author: dr-rompecabezas
---

# Django Project Scaffolding

Read [reference.md](reference.md) in full before doing anything else. Then run the question flow below.

---

## Assumed Starting Point

The user is **already inside the project directory** with:
- `uv init` done — `pyproject.toml` exists
- `.venv` created and activated
- `django` (or `django` + `wagtail`) already installed via `uv add`

Do not generate setup instructions for creating the directory or installing Django. Do not `cd` anywhere.

---

## Question Flow

Ask these questions **one at a time**, in order. Wait for each answer before asking the next.

**Q1 — Project type:**
- A. Django full-stack — templates + Tailwind CSS, server-rendered UI
- B. Wagtail CMS full-stack — same as A but with Wagtail
- C. Django REST API — DRF only. No Django templates beyond admin.

**Q2 — Project name:**
Used for pyproject.toml name, Railway service, and database name.
Derive the Python package name automatically: replace hyphens with underscores.
*Example: `my-project` → package name: `my_project`*

**Q3 — Main app name:**
The primary local Django app beyond `users` (default: `core`).
Apps live at the project root — flat structure, no `apps/` subdirectory.

**Q4 — Celery + Redis?** (yes / no)
Recommend yes only if: background jobs >5s, retry logic, scheduled tasks, or high-volume async email. Single transactional emails do NOT need Celery.

**Q5 [Full-stack and Wagtail only] — Frontend interactivity?** (select any, default: both)
Both HTMX and Alpine.js are served from `static/js/` as downloaded minified files — no CDN, no pip package required.
- HTMX — server-driven interactions; place `htmx.min.js` in `static/js/`
- Alpine.js — client-side reactivity; place `alpine.min.js` in `static/js/`
- Both (recommended default)
- Neither

**Q6 — Authentication:**
- A. django-allauth — recommended default; handles email + password
- B. django-allauth with Google OAuth — adds `[socialaccount]` and Google provider
- C. Built-in Django auth only — simple projects, internal tools

**Q7 — Languages?** (default: English only)
List only the languages the project actually needs. English is always included.
Common additions: French (`fr`), Spanish (`es`), Portuguese (`pt`).
If English only, no extra i18n setup is generated. If multiple, activates `LANGUAGES` and `LOCALE_PATHS` in `base.py`.

Once you have all seven answers, generate the complete project without further questions.

---

## Version Resolution

Before generating any files, look up the current versions using web search or available tools:

- **Python** — latest stable release (e.g. python.org/downloads)
- **Node.js** — latest LTS release (e.g. nodejs.org)
- **Django** — latest LTS release, which is always the x.2 series (e.g. djangoproject.com/download)

Use these resolved versions in `railpack.json` (`packages.python`, `packages.node`), `pyproject.toml` (`target-version`, `python_version`), and anywhere else a version string appears. Do not fall back to values from your training data.

---

## Generation Checklist

Generate **every file** in the list below. Use reference.md for all non-trivial templates.
For files marked *standard*, generate from Django conventions.
Substitute `{project_slug}` (kebab-case), `{project_name}` (snake_case), `{app_name}` throughout.

**Root**
- `manage.py` — *standard*; `DJANGO_SETTINGS_MODULE = "config.settings.local"`
- `pyproject.toml` — **update the existing file**: update `[project]` name/description; add all `[tool.*]` sections from reference.md § pyproject.toml. Do NOT replace dependencies — those are managed by `uv add`.
- `.env` — reference.md § .env.example; substitute `{project_name}`; this is the working local file
- `.env.example` — same as .env (committed; no real secrets)
- `.gitignore` — *standard* Django gitignore (include `.env`, `staticfiles/`, `media/`, `*.pyc`, `node_modules/`, etc.)
- `.pre-commit-config.yaml` — reference.md § Pre-commit; omit `djlint` hooks for API-only (Q1=C)
- `railpack.json` — reference.md § Deployment: railpack.json; if Q7 has multiple languages, add `gettext` apt packages (reference.md § Multilingual projects)
- `railway.json` — reference.md § Deployment: railway.json; omit npm build step for API-only (Q1=C); if Q7 has multiple languages, add `compilemessages` to startCommand (reference.md § Multilingual projects)
- `docker-compose.yml` — reference.md § docker-compose; uncomment Redis/Celery/Flower if Q4=yes
- `package.json` — reference.md § package.json; full-stack and Wagtail only (Q1=A or B)
- `assets/css/input.css` — `@import "tailwindcss";`; full-stack and Wagtail only; input lives in assets/, NOT static/
- `tailwind.config.js` — **do not generate**; Tailwind v4 does not need one

**config/**
- `config/__init__.py` — import celery_app if Q4=yes (reference.md § Celery); else empty
- `config/asgi.py` — *standard*; `DJANGO_SETTINGS_MODULE = "config.settings.local"`
- `config/wsgi.py` — *standard*; `DJANGO_SETTINGS_MODULE = "config.settings.local"`
- `config/celery.py` — reference.md § Celery; only if Q4=yes; substitute `{project_name}`
- `config/urls.py` — reference.md § config/urls.py; include admin, allauth (if Q6=A or B), and app URLs
- `config/settings/__init__.py` — empty
- `config/settings/base.py` — reference.md § base.py; customize INSTALLED_APPS per all answers; add `LANGUAGES` + `LOCALE_PATHS` only if Q7 has more than one language
- `locale/.gitkeep` — only if Q7 has more than one language; empty placeholder so the directory is committed and `compilemessages` doesn't error on first deploy
- `config/settings/local.py` — reference.md § local.py
- `config/settings/production.py` — reference.md § production.py (use in full, do not abbreviate)

**.github/**
- `.github/workflows/ci.yml` — reference.md § GitHub Actions; add Redis service if Q4=yes

**users/** (always included)
- `users/__init__.py` — empty
- `users/apps.py` — *standard* AppConfig; `name = "users"`
- `users/models.py` — reference.md § Authentication → users/models.py
- `users/admin.py` — reference.md § users app — admin.py
- `users/migrations/__init__.py` — empty
- `users/tests/__init__.py` — empty
- `users/tests/factories.py` — reference.md § UserFactory
- `users/tests/test_users.py` — reference.md § Authentication → users/tests/test_users.py
- `users/views.py` — empty initially

**{app_name}/** (main app)
- `{app_name}/__init__.py` — empty
- `{app_name}/apps.py` — *standard* AppConfig; `name = "{app_name}"`
- `{app_name}/admin.py` — empty initially
- `{app_name}/models.py` — empty initially
- `{app_name}/urls.py` — empty urlpatterns list
- `{app_name}/views.py` — empty initially
- `{app_name}/migrations/__init__.py` — empty
- `{app_name}/tests/__init__.py` — empty

**templates/** (full-stack and Wagtail only, Q1=A or B)
- `templates/base.html` — minimal HTML5 boilerplate; Tailwind CSS link (`{% static 'css/tailwind.css' %}`); HTMX script tag from static if selected (`{% static 'js/htmx.min.js' %}`); Alpine.js script tag from static if selected (`{% static 'js/alpine.min.js' %}`); `{% block content %}{% endblock %}`

---

## After Generating All Files

Print a **Next Steps** section:

```
Install dependencies (run uv add for each you need):
  Core:      uv add gunicorn whitenoise dj-database-url psycopg[binary] django-environ
  Auth:      uv add django-allauth          # or django-allauth[socialaccount]
  Media:     uv add django-storages[s3] boto3
  Email:     uv add django-anymail[mailtrap]
  Monitoring: uv add sentry-sdk[django]
  API only:  uv add djangorestframework drf-spectacular djangorestframework-simplejwt django-cors-headers django-filter
  Wagtail:   already installed; add uv add django-allauth if needed
  Celery:    uv add celery[redis] django-celery-beat flower  # only if Q4=yes

Dev dependencies:
  uv add --dev django-debug-toolbar pytest pytest-django factory-boy faker coverage[toml] django-stubs[compatible-mypy] mypy pre-commit

Download static JS files (if HTMX or Alpine selected):
  curl -o static/js/htmx.min.js https://unpkg.com/htmx.org/dist/htmx.min.js
  curl -o static/js/alpine.min.js https://unpkg.com/alpinejs/dist/cdn.min.js

Setup:
  cp .env.example .env
  # Set SECRET_KEY: python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
  docker compose up -d
  uv run pre-commit install
  python manage.py migrate
  python manage.py createsuperuser

Frontend (full-stack/Wagtail only):
  npm install
  npm run dev:css   ← keep running in a separate terminal

Railway deployment — set in Railway dashboard:
  SECRET_KEY, ALLOWED_HOSTS, DJANGO_SETTINGS_MODULE=config.settings.production
  DATABASE_URL and REDIS_URL are auto-injected by Railway plugins
  SENTRY_DSN, MAILTRAP_API_TOKEN, AWS_* — set when ready
```

---
> Source: [dr-rompecabezas/django-scaffold-skill](https://github.com/dr-rompecabezas/django-scaffold-skill) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->

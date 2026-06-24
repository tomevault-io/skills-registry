---
name: django-admin
description: Django admin customization patterns for Nudge. Use when modifying the admin panel, adding models to admin, or changing admin branding/styling. Triggers when the user asks about Django admin, admin panel, or model registration. Use when this capability is needed.
metadata:
  author: cibrandocampo
---

# Django Admin — Nudge

## Branding

The admin is branded to match Nudge's zinc-neutral + yellow-accent palette via a custom template:
`backend/templates/admin/base_site.html`

This template:
- Extends `admin/base.html` (NOT `admin/base_site.html` — that would loop)
- Overrides Django's CSS variables for both light and dark mode
- Sets the "Nudge" logo linking to `/` (the app, not `/admin/`)
- Forces white text on dark headers in all theme modes
- Repaints the "save" (default) submit button with the brand yellow (`#fcd34d`) so the primary CTA matches the app

### Color palette

Mirrors `frontend/src/index.css` — **source of truth lives there**. If it changes, update this template too.

| Token           | Light      | Dark       |
|-----------------|------------|------------|
| Primary (text / bg dark) | `#18181b` | `#fafafa` |
| Primary hover   | `#27272a`  | `#e4e4e7`  |
| Accent (brand)  | `#fcd34d`  | `#fcd34d`  |
| Body bg         | `#fafafa`  | `#0a0a0b`  |
| Surface         | `#ffffff`  | `#18181b`  |
| Border          | `#e4e4e7`  | `#27272a`  |
| Link            | `#09090b`  | `#fafafa`  |
| Link hover      | `#27272a`  | `#fcd34d`  |

### Django CSS variables to override

The key to theming Django admin is overriding its own CSS custom properties:
`--header-bg`, `--module-bg`, `--link-fg`, `--button-bg`, `--breadcrumbs-bg`, etc.
Do this for `:root`, `html[data-theme="light"]`, `html[data-theme="dark"]`,
and `@media (prefers-color-scheme: dark)` with `:root:not([data-theme="light"])`.

## Admin access from the PWA

The frontend has an "Admin" button (staff users only) in the Header component.
It works by creating a hidden form that POSTs the JWT token to `/api/auth/admin-access/`.
The backend view (`apps.users.views.admin_access`):
- Is `@csrf_exempt` (no CSRF token available from the SPA)
- Validates the JWT, checks `is_staff`
- Creates a Django session via `django_login()`
- Redirects to `/admin/`

## i18n

Django admin is multilingual out of the box. We enabled it by:
- Adding `django.middleware.locale.LocaleMiddleware` to `MIDDLEWARE`
  (after `SessionMiddleware`, before `CommonMiddleware`)
- Setting `LANGUAGES = [("en", "English"), ("es", "Español"), ("gl", "Galego")]`

The admin auto-detects language from the browser's `Accept-Language` header.

## Registering models

Pattern used in `apps/users/admin.py`:

```python
@admin.register(User)
class UserAdmin(BaseUserAdmin):
    fieldsets = BaseUserAdmin.fieldsets + (
        ("Nudge settings", {"fields": ("timezone", "daily_notification_time", "language")}),
    )
    list_display = ["username", "email", "timezone", "is_staff", "is_active"]
    list_filter = ["is_staff", "is_active", "timezone"]
    search_fields = ["username", "email"]
```

---
> Source: [cibrandocampo/nudge](https://github.com/cibrandocampo/nudge) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->

---
name: django-forge
description: Generate production-ready Django + DRF APIs following Controller → Repository → Serializer pattern with Celery Use when this capability is needed.
metadata:
  author: vikisingh23
---

Generate Django + DRF APIs following Controller → Repository → Serializer pattern.

## What it generates
Model (audit fields + history) + Repository (extends BaseRepository) +
Serializer (DRF validation) + Controller (@api_view + decorators) +
URLs + Celery tasks + Tests + Migration.

Uses: django-simple-history, django-filter, django-redis.

For full instructions, read `agents/django-forge.md`.

---
> Source: [vikisingh23/neuraforge-ai](https://github.com/vikisingh23/neuraforge-ai) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->

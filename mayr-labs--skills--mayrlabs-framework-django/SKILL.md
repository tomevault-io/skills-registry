---
name: mayrlabs-framework-django
description:
  Django rules focused on class-based views, clean ORM usage, and decoupling fat
  models where appropriate.
license: MIT
metadata:
  author: MayR Labs
  version: '1.0'
---

# MayR Labs Django Doctrine

## Purpose

To maintain robust, predictable Django architectures by enforcing clear patterns
over raw implicit magic, and preventing the "fat models, fat views"
anti-pattern.

## Audience

AI Agents generating Django views, serializers, and models.

## Core Rules & Constraints

### 1. Views & Routing

- Leverage Class-Based Views (CBVs) or ViewSets (in Django Rest Framework).
  Avoid sprawling function-based views containing complex logic.
- Do not place heavy multi-step business logic inside a View. Abstract it into a
  `services.py` or `selectors.py` pattern (akin to separating queries from
  mutations).

### 2. ORM Optimization

- **N+1 Prevention**: Radically enforce the use of `select_related()` (for
  foreign keys) and `prefetch_related()` (for many-to-many/reverse relations) on
  QuerySets that will be serialized or iterated over.

### 3. Security

- Never expose internal database fields via `fields = '__all__'` on generic
  Serializers or Forms. Fields must be explicitly listed.

## Inputs & Outputs

- **Input**: Django `views.py`, `models.py`, `serializers.py`.
- **Output**: Service-layered, optimized, and explicit Django architectures.

---
> Source: [MayR-Labs/skills](https://github.com/MayR-Labs/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->

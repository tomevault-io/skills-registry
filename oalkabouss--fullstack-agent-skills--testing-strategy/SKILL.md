---
name: testing-strategy
description: Testing strategy for backend use-cases and frontend hooks/UI Use when this capability is needed.
metadata:
  author: oalkabouss
---
## What I do

Je propose une stratégie de tests cohérente full-stack.

## Backend
- Domain : tests purs.
- Use-cases : tests unitaires avec ports mockés.
- Infra : intégration ciblée.

## Frontend
- Hooks : tests avec QueryClient isolé + mocks services.
- UI : tests d'interaction et accessibilité.

## Mock philosophy
- Préférer des fakes typés (implémentations d'interface) plutôt que des mocks dynamiques.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oalkabouss) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

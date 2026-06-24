---
name: django-fat-model
description: Lógica de negócio no model ou em services; views finas Use when this capability is needed.
metadata:
  author: alanpcf
---
- Lógica de negócio em métodos do model ou em `services.py` por app.
- Views finas, só orquestram (autenticar, validar, chamar service, renderizar).
- `select_related`/`prefetch_related` em **toda** query com relação.
- Managers customizados pra query reutilizada.
- Forms ou DRF serializers pra validação, nunca no view.
- Sinais (`post_save`, etc) com moderação — escondem fluxo.

---
> Source: [alanpcf/cocada](https://github.com/alanpcf/cocada) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->

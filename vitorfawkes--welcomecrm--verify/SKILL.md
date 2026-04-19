---
name: verify
description: Verificação completa de qualidade do projeto (security, lint, schema, tests, UX) Use when this capability is needed.
metadata:
  author: vitorfawkes
---

Rode a verificação completa de qualidade:

1. `python3 .agent/scripts/checklist.py .`
2. Analise cada resultado (P0-Security até P5-SEO)
3. Se algum check falhar, corrija o problema
4. Rode novamente até tudo passar
5. Reporte resultado final ao usuário

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vitorfawkes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

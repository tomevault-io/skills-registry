---
name: validar-diversidad
description: > Use when this capability is needed.
metadata:
  author: alvaretto
---

> **ROUTING**: Este skill tiene `model_recommendation: haiku`. Claude DEBE delegarlo via `Task(subagent_type="general-purpose", model="haiku")` pasando las instrucciones completas y la ruta del archivo .Rmd como contexto. Ver regla `.claude/rules/modelo-routing-obligatorio.md`.

# Instrucciones de Validación

1. Una vez generado el archivo .Rmd en la carpeta `/A-Produccion/`, localiza su 
correspondiente test en `tests/testthat/` [2].

2. Ejecuta el comando local: `Rscript -e 'testthat::test_file("ruta/del/test.R")'`.
3. Informa al usuario si el ejercicio cumple con el criterio de aleatorización 
completa definido en la filosofía del sistema [3, 4].

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alvaretto) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

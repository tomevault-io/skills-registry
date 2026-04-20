---
name: api-inasistig
description: Lógica de servidor PHP y consultas MySQL para el sistema inasistig. Use when this capability is needed.
metadata:
  author: ceslep
---

## Qué hago

- Creación de endpoints PHP que retornan JSON.
- Consultas SQL para la tabla `examenes`, `estudiantes` y `asistencias`.

## Reglas de Oro

- **Siempre** usar PDO con Prepared Statements.
- Validar que el `id_docente` esté presente en cada petición.
- Formatear las respuestas como: `{"status": "success", "data": [...]}`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ceslep) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

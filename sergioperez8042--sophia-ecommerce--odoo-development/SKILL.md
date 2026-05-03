---
name: odoo-development
description: Habilidad para desarrollar soluciones empresariales en Odoo siguiendo arquitectura modular, buenas prácticas oficiales, testing y CI/CD. Use when this capability is needed.
metadata:
  author: sergioperez8042
---

## Descripción técnica

Odoo es un ERP modular basado en Python. Cada módulo encapsula modelos, vistas, seguridad, datos y pruebas.

### Estructura recomendada de un módulo

my_module/
models/
views/
security/
data/
reports/
tests/
manifest.py

### Buenas prácticas clave

- **Modelos**
  - Clases en `PascalCase`
  - `_name` en `snake.case`
- **ORM**
  - Evitar SQL directo si no es necesario
  - No usar `cr.commit` en lógica ni tests
- **Vistas**
  - XML claro y desacoplado
  - QWeb para reportes
- **Testing**
  - `TransactionCase`
  - Tests en `tests/test_*.py`
- **CI/CD**
  - Odoo.sh o pipelines propios
- **Arquitectura**
  - MVC: Modelos (Python), Vistas (XML/QWeb), Controladores (HTTP)

El desarrollo profesional en Odoo requiere alinearse estrictamente con las guías oficiales para garantizar mantenibilidad y compatibilidad futura.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sergioperez8042) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

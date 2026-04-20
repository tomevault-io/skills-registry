---
name: creador-de-habilidades
description: Utilidad para que el agente cree nuevas habilidades (skills) dentro del proyecto de forma estructurada y en español. Use when this capability is needed.
metadata:
  author: mens-git
---

# Skill: Creador de Habilidades

Esta habilidad permite al agente diseñar y crear nuevas extensiones de capacidad (Skills) para sí mismo, siguiendo los estándares de Antigravity.

## Contexto
Activa esta habilidad cuando el usuario solicite crear una nueva "Skill", "Habilidad" o cuando identifiques que una tarea compleja se beneficiaría de un conjunto de instrucciones especializadas y reutilizables.

## Guía de Creación

### 1. Ubicación y Estructura
Todas las habilidades deben residir en la carpeta `.agent/skills/` en la raíz del proyecto.
La estructura de una habilidad (ej. `mi-habilidad`) es:
- `.agent/skills/mi-habilidad/SKILL.md` (Obligatorio)
- `.agent/skills/mi-habilidad/scripts/` (Opcional: Scripts de ayuda)
- `.agent/skills/mi-habilidad/examples/` (Opcional: Ejemplos de referencia)
- `.agent/skills/mi-habilidad/resources/` (Opcional: Plantillas o documentación)

### 2. El archivo SKILL.md
El archivo debe comenzar con un bloque YAML de metadatos:
```yaml
---
name: nombre-de-la-habilidad
description: Descripción concisa indicando qué hace y cuándo usarla.
---
```

### 3. Redacción de Instrucciones
- Escribir siempre en **tercera persona** (ej. "El agente deberá...", "Se debe asegurar que...").
- Usar **Markdown** claro y estructurado.
- Definir **Reglas Críticas** y **Pasos de Trabajo (Workflows)**.
- El idioma principal para las instrucciones dentro de esta habilidad de creación es el **Español**.

## Reglas de Oro
- **Especialización**: Cada habilidad debe enfocarse en un dominio específico. No crees "habilidades generales".
- **Descubrimiento**: La descripción en el YAML es vital; debe ser clara para que el sistema sepa cuándo cargar la habilidad.
- **Calidad**: Incluye siempre una sección de mejores prácticas o ejemplos de "buen resultado".

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mens-git) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

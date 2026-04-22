---
name: skill-architect
description: Diseña, estructura y genera nuevas Skills para Antigravity. Úsala cuando quieras crear una nueva herramienta, automatizar un flujo de trabajo recurrente o extender las capacidades del IDE. Use when this capability is needed.
metadata:
  author: salomonsv81
---
# Skill Architect

## Objetivo
Actuar como un ingeniero senior de herramientas (Tooling Engineer) para crear nuevas "Skills" dentro del ecosistema Google Antigravity. Tu trabajo es tomar una intención abstracta del usuario y convertirla en una estructura de archivos concreta y funcional que cumpla con los estándares de la documentación oficial.

## Capacidades
1. **Interpretación de Scope:** Determinar si una skill debe ser global (para el usuario) o workspace (para el repositorio).
2. **Generación de Archivos:** Utilizar el script `builder.py` para crear directorios y archivos físicos.
3. **Redacción Técnica:** Escribir el contenido del archivo `SKILL.md` de la nueva skill, asegurando prompts claros para Gemini 3.

## Instrucciones Paso a Paso

### 1. Análisis de Requerimientos
Cuando el usuario solicite una nueva skill, analiza:
* **Nombre:** Si no se provee, sugiere uno en `kebab-case` (ej: `log-parser`, `git-semantic-commit`).
* **Propósito:** ¿Qué problema resuelve?
* **Alcance (Scope):**
    * Si es útil para múltiples proyectos -> `Global`
    * Si depende de archivos del proyecto actual -> `Workspace`

### 2. Ejecución del Builder
Invoca el script `builder.py` con los parámetros adecuados.
* Argumentos: `--name`, `--description`, `--scope` (local/global).
* **Comando:** `python3 scripts/builder.py --name "{skill_name}" --description "{short_description}" --scope "{local|global}"`

### 3. Personalización del SKILL.md
El script `builder.py` genera una plantilla básica. Tu tarea es **sobrescribir** el archivo `SKILL.md` generado con instrucciones específicas para la nueva skill.

## Restricciones y Seguridad
* NO sobrescribas skills existentes sin confirmación explícita.
* NO generes skills que ejecuten `rm -rf` o comandos destructivos sin pedir confirmación.
* Siempre genera una sección de `# Security Constraints` en la nueva skill.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/salomonsv81) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

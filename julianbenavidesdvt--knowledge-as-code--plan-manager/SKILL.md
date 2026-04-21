---
name: plan-manager
description: Skill para gestionar la configuración inicial del plan de implementación en flujos de trabajo de Specify. Use when this capability is needed.
metadata:
  author: julianbenavidesdvt
---

# Gestor de Plan de Implementación

Esta skill permite al agente configurar el entorno de planificación manualmente.

## Instrucciones

### 1. Verificar Entorno de Rama (Feature Branch)

1.  **Obtener rama actual**:
    Ejecuta: `git branch --show-current`
2.  **Validar formato**:
    Asegúrate de que la rama sigue el patrón `[número]-[nombre-corto]` (ej. `5-auth-usuario`).
    *Si no estás en una rama de funcionalidad, detente y solicita al usuario que cree una primero.*

### 2. Definir Rutas y Directorios

Basado en el nombre de la rama actual `[BRANCH_NAME]`:

1.  **Directorio de la Característica (FEATURE_DIR)**:
    `specs/[BRANCH_NAME]/`
2.  **Archivo del Plan (IMPL_PLAN)**:
    `specs/[BRANCH_NAME]/plan.md`
    *(O `plan_es.md` si el usuario prefiere español)*
3.  **Archivo de Especificación (FEATURE_SPEC)**:
    `specs/[BRANCH_NAME]/spec.md`

### 3. Crear Directorio

1.  **Crear carpeta**:
    ```bash
    mkdir -p "specs/[BRANCH_NAME]"
    ```

### 4. Inicializar Plan desde Plantilla

1.  **Localizar Plantilla**:
    Busca en `.specify/templates/plan-template.md`.
    *Si no existe, busca en `templates/plan-template.md`.*

2.  **Copiar Plantilla**:
    Si el archivo `plan.md` (o `plan_es.md`) NO existe:
    ```bash
    cp ".specify/templates/plan-template.md" "specs/[BRANCH_NAME]/plan.md"
    ```
    *Si la plantilla no existe, crea un archivo vacío o con estructura básica.*

### 5. Reportar Estado

Informa al usuario las rutas configuradas:
- **FEATURE_SPEC**: `specs/[BRANCH_NAME]/spec.md`
- **IMPL_PLAN**: `specs/[BRANCH_NAME]/plan.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/julianbenavidesdvt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

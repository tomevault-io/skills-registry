---
name: git-manager
description: Skill para gestionar operaciones de Git (ramas, checkout, commits, push) y reemplazar scripts externos en flujos de trabajo. Use when this capability is needed.
metadata:
  author: julianbenavidesdvt
---

# Gestor de Operaciones Git

Esta skill proporciona procedimientos estandarizados para manejar operaciones de Git directamente mediante el agente, eliminando la dependencia de scripts de shell externos como `create-new-feature.sh`.

## Instrucciones

### 1. Creación de Rama de Funcionalidad (Feature Branch)

Para crear una nueva rama de funcionalidad siguiendo el patrón numérico `[numero]-[nombre-corto]`:

1.  **Actualizar referencias remotas**:
    Ejecuta siempre esto primero para asegurar que tienes la información más reciente.
    ```bash
    git fetch --all --prune
    ```

2.  **Determinar el siguiente número de rama**:
    Debes buscar el número más alto existente asociado al `nombre-corto` en tres fuentes:
    
    a. **Ramas Remotas**:
       Ejecuta: `git ls-remote --heads origin`
       Busca patrones como: `refs/heads/[numero]-[nombre-corto]`
    
    b. **Ramas Locales**:
       Ejecuta: `git branch`
       Busca patrones como: `[numero]-[nombre-corto]`
    
    c. **Directorios de Especificaciones** (si aplica):
       Listar el directorio `specs/` (si existe) y buscar carpetas que coincidan con `[numero]-[nombre-corto]`.

    **Cálculo**:
    *   Extrae los números encontrados.
    *   Encuentra el máximo `N`.
    *   El nuevo número será `N + 1`.
    *   Si no hay coincidencias, empieza con `1`.

3.  **Crear y cambiar a la nueva rama**:
    ```bash
    git checkout -b <nuevo-numero>-<nombre-corto>
    ```

### 2. Guardar Cambios (Checkout & Commit)

1.  **Verificar estado**:
    ```bash
    git status
    ```
    *Asegúrate de estar en la rama correcta.*

2.  **Añadir archivos**:
    ```bash
    git add <ruta_archivo>
    ```
    *Usa `.` solo si estás seguro de que todos los cambios deben incluirse.*

3.  **Realizar Commit**:
    ```bash
    git commit -m "<mensaje descriptivo>"
    ```

### 3. Publicar Cambios (Push)

1.  **Enviar a remoto**:
    ```bash
    git push -u origin <nombre-rama>
    ```
    *La bandera `-u` establece el upstream para la rama.*

## Integración con specify.md / specify_es.md

Para integrar esta skill en `specify.md` o `specify_es.md`, sustituye la llamada a scripts externos (`create-new-feature.sh`) por los pasos descritos en la sección "Creación de Rama de Funcionalidad".

**En lugar de:**
> Ejecuta el script `{SCRIPT}`...

**La instrucción en el workflow debe ser:**
> Usa la skill `git_manager` para determinar el siguiente número de rama disponible y crear la rama manualmente usando `run_command`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/julianbenavidesdvt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

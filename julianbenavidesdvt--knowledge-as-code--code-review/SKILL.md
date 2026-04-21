---
name: code-review
description: Skill para realizar revisiones de código automatizadas siguiendo estándares de calidad y mejores prácticas. Use when this capability is needed.
metadata:
  author: julianbenavidesdvt
---

# Gestor de Code Review

Esta skill proporciona capacidades de revisión de código automatizada, evaluando calidad, seguridad, rendimiento, y adherencia a estándares del proyecto.

## Instrucciones

### 1. Revisar Cambios de Feature (Review Feature Changes)

Utiliza esta función para revisar todos los cambios de una feature antes de merge.

1. **Identificar Cambios**:
   ```bash
   # Obtener archivos modificados respecto a main
   git diff --name-only main...HEAD

   # Ver cambios detallados
   git diff main...HEAD
   ```

2. **Categorizar Archivos**:
   - **Código fuente**: `src/**/*.ts`, `src/**/*.py`, etc.
   - **Tests**: `tests/**/*`, `*.spec.ts`, `*.test.ts`
   - **Configuración**: `*.json`, `*.yaml`, `*.config.*`
   - **Documentación**: `*.md`, `docs/**/*`

3. **Ejecutar Checklist por Categoría**:

   **Para cada archivo de código fuente**:
   - [ ] Sigue convenciones de naming del proyecto
   - [ ] No contiene código comentado
   - [ ] No tiene console.log/print de debug
   - [ ] Maneja errores apropiadamente
   - [ ] Tiene tipos explícitos (no `any`)
   - [ ] Funciones tienen responsabilidad única
   - [ ] No hay duplicación de código

   **Para cada archivo de test**:
   - [ ] Sigue patrón AAA
   - [ ] Nombre describe comportamiento
   - [ ] No tiene lógica compleja
   - [ ] Assertions son específicas

### 2. Análisis de Seguridad (Security Analysis)

Evalúa código contra vulnerabilidades OWASP Top 10.

1. **Verificar Inputs**:
   ```typescript
   // ❌ Malo - Sin validación
   const user = await db.query(`SELECT * FROM users WHERE id = ${req.params.id}`);

   // ✅ Bueno - Parametrizado
   const user = await db.query('SELECT * FROM users WHERE id = $1', [req.params.id]);
   ```

2. **Checklist de Seguridad**:
   - [ ] No hay SQL injection (queries parametrizadas)
   - [ ] No hay XSS (output escapeado)
   - [ ] Secrets no están hardcodeados
   - [ ] Autenticación validada en endpoints
   - [ ] Autorización verificada por recurso
   - [ ] Input validado y sanitizado
   - [ ] No hay información sensible en logs
   - [ ] CORS configurado correctamente

3. **Patrones a Detectar**:
   | Patrón | Riesgo | Acción |
   |--------|--------|--------|
   | `eval()`, `Function()` | Crítico | Rechazar |
   | `innerHTML =` | Alto | Revisar contexto |
   | Passwords en código | Crítico | Rechazar |
   | `dangerouslySetInnerHTML` | Alto | Justificar uso |

### 3. Análisis de Rendimiento (Performance Analysis)

Identifica problemas de rendimiento potenciales.

1. **Patrones Problemáticos**:

   **N+1 Queries**:
   ```typescript
   // ❌ N+1 - Una query por item
   for (const user of users) {
     const orders = await db.orders.find({ userId: user.id });
   }

   // ✅ Batch - Una sola query
   const orders = await db.orders.find({ userId: { $in: userIds } });
   ```

   **Memory Leaks**:
   ```typescript
   // ❌ Event listener no removido
   useEffect(() => {
     window.addEventListener('resize', handler);
   }, []);

   // ✅ Cleanup incluido
   useEffect(() => {
     window.addEventListener('resize', handler);
     return () => window.removeEventListener('resize', handler);
   }, []);
   ```

2. **Checklist de Rendimiento**:
   - [ ] No hay N+1 queries
   - [ ] Loops no hacen operaciones async innecesarias
   - [ ] Datos grandes están paginados
   - [ ] Caching implementado donde aplica
   - [ ] No hay re-renders innecesarios (React/Angular)
   - [ ] Subscriptions se cancelan en cleanup

### 4. Análisis de Mantenibilidad (Maintainability)

Evalúa la calidad estructural del código.

1. **Métricas a Evaluar**:
   - **Complejidad Ciclomática**: < 10 por función
   - **Líneas por función**: < 50
   - **Parámetros por función**: < 5
   - **Profundidad de anidación**: < 4 niveles

2. **Code Smells a Detectar**:
   | Smell | Ejemplo | Recomendación |
   |-------|---------|---------------|
   | God Class | Clase > 500 líneas | Dividir responsabilidades |
   | Long Method | Función > 50 líneas | Extraer funciones |
   | Feature Envy | Accede más a otro objeto | Mover método |
   | Primitive Obsession | Muchos strings/números | Crear value objects |
   | Shotgun Surgery | Cambio afecta muchos archivos | Consolidar lógica |

### 5. Verificar Adherencia a Arquitectura (Architecture Check)

Valida que el código sigue la arquitectura definida.

1. **Leer plan.md**:
   - Identificar arquitectura definida (Layered, Clean, etc.)
   - Extraer convenciones de estructura de archivos

2. **Validar Dependencias**:
   ```
   Controllers → Services → Repositories → Entities
         ↓            ↓            ↓
        DTOs       Interfaces    Models

   ❌ Controller NO debe acceder directamente a Repository
   ❌ Service NO debe importar Controller
   ```

3. **Checklist de Arquitectura**:
   - [ ] Imports siguen dirección correcta
   - [ ] No hay dependencias circulares
   - [ ] Archivos en carpetas correctas según plan.md
   - [ ] DTOs usados en boundaries
   - [ ] Entidades no expuestas directamente

### 6. Generar Reporte de Review (Generate Report)

Crea documentación estructurada del code review.

```markdown
# Code Review Report

## Metadata
- **Feature**: [branch-name]
- **Reviewer**: AI Agent
- **Fecha**: [YYYY-MM-DD]
- **Archivos Revisados**: X

## Resumen Ejecutivo

| Categoría | Issues | Críticos | Warnings |
|-----------|--------|----------|----------|
| Seguridad | X | X | X |
| Rendimiento | X | X | X |
| Mantenibilidad | X | X | X |
| Arquitectura | X | X | X |

**Decisión**: ✅ APPROVE / ⚠️ REQUEST CHANGES / ❌ REJECT

## Issues Encontrados

### Críticos (Bloquean Merge)
1. **[SEC-001]** SQL Injection en `user.service.ts:45`
   - **Línea**: `db.query(\`SELECT * FROM users WHERE id = ${id}\`)`
   - **Riesgo**: Ejecución de SQL arbitrario
   - **Solución**: Usar queries parametrizadas

### Warnings (Deben Resolverse)
1. **[PERF-001]** N+1 Query en `orders.service.ts:78`

### Sugerencias (Opcionales)
1. **[MAINT-001]** Función `processData` tiene 65 líneas

## Archivos Revisados

| Archivo | Líneas | Issues | Estado |
|---------|--------|--------|--------|
| user.service.ts | +120 | 2 | ⚠️ |
| user.controller.ts | +45 | 0 | ✅ |
| user.spec.ts | +80 | 0 | ✅ |

## Próximos Pasos
- [ ] Resolver issues críticos
- [ ] Resolver warnings
- [ ] Re-solicitar review
```

## Integración con Flujo de Trabajo

```
/implement → (código escrito) → code_review → /test → merge
                                     ↓
                              Reporte generado
                                     ↓
                              Issues listados
                                     ↓
                              Desarrollador corrige
                                     ↓
                              Re-review
```

## Comandos Útiles

```bash
# Ver cambios en branch actual vs main
git diff main...HEAD

# Ver solo archivos cambiados
git diff --name-only main...HEAD

# Ver estadísticas de cambios
git diff --stat main...HEAD

# Ver commits en la feature branch
git log main..HEAD --oneline
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/julianbenavidesdvt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

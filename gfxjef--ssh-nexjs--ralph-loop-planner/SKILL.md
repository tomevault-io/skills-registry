---
name: ralph-loop-planner
description: Generador de planes de implementación estructurados para ejecutar con el plugin ralph-loop de Claude Code. Usar cuando el usuario necesite crear un archivo PLAN_*.md con instrucciones paso a paso para implementar features, refactorizaciones, migraciones o nuevos módulos. Detecta el tipo de proyecto (Dashboard, API, Full Stack, Chat/AI) y genera preguntas de clarificación antes de crear el plan final con comando ralph-loop listo para copiar. Use when this capability is needed.
metadata:
  author: gfxjef
---

# Ralph-Loop Planner

Skill para generar planes de implementación ejecutables con ralph-loop.

## Flujo de Trabajo

```
1. RECIBIR RESUMEN → Usuario describe qué necesita
2. DETECTAR TIPO   → Identificar tipo de proyecto
3. PREGUNTAR      → Hacer preguntas de clarificación según tipo
4. GENERAR PLAN   → Crear archivo PLAN_*.md estructurado
5. GENERAR CMD    → Comando ralph-loop listo para copiar
```

## Tipos de Proyecto y Preguntas

### Dashboard (React + FastAPI/Python)
- ¿Qué datos/estadísticas se mostrarán?
- ¿Hay filtros o parámetros dinámicos?
- ¿Se conecta a base de datos existente o nueva?
- ¿Requiere gráficos (SPC, Pareto, líneas de tiempo)?
- ¿Hay validaciones o fórmulas matemáticas?

### API/Microservicio (FastAPI o Node.js Express)
- ¿Cuáles son los endpoints principales?
- ¿Qué entidades/modelos de datos maneja?
- ¿Requiere autenticación? ¿Qué tipo?
- ¿Se integra con servicios externos?
- ¿Hay operaciones CRUD específicas?

### Full Stack (Next.js + Node.js + DB)
- ¿Cuál es el flujo principal del usuario?
- ¿Qué páginas/vistas necesita?
- ¿Estructura de base de datos (tablas, relaciones)?
- ¿Hay formularios complejos?
- ¿Requiere estado global o contextos?

### Chat/AI (Gemini/Claude + Backend)
- ¿Qué modelo de AI usará?
- ¿Hay context caching o system prompts?
- ¿Maneja historial de conversaciones?
- ¿Requiere herramientas/funciones para el modelo?
- ¿Hay límites de tokens o rate limiting?

## Estructura del Plan Generado

El archivo `PLAN_[NOMBRE].md` debe seguir esta estructura:

```markdown
# Plan: [Nombre del Feature/Proyecto] v[X.X]

## Resumen
[Descripción breve de lo que se implementará]

## Prerequisitos
- [ ] [Dependencias necesarias]
- [ ] [Accesos o configuraciones previas]

---

## SECCIÓN 1: BASE DE DATOS (si aplica)
### 1.1 Migraciones
[Instrucciones específicas]

### 1.2 Seeds/Datos iniciales
[Si se requieren]

---

## SECCIÓN 2: BACKEND
### 2.1 Modelos/Esquemas
[Definiciones]

### 2.2 Endpoints/Rutas
[Lista de endpoints con método y descripción]

### 2.3 Servicios/Lógica de negocio
[Funciones principales]

### 2.4 Tests Backend (OBLIGATORIO)
Crear en `/tests_temp/backend/`:
- [Test específico 1 según tipo de proyecto]
- [Test específico 2 según tipo de proyecto]
- [Test específico N según tipo de proyecto]

**Ejecutar tests y verificar que pasen antes de continuar.**

---

## SECCIÓN 3: FRONTEND
### 3.1 Componentes
[Lista de componentes a crear/modificar]

### 3.2 Páginas/Vistas
[Rutas y layouts]

### 3.3 Integración con API
[Llamadas al backend]

### 3.4 Tests Frontend (OBLIGATORIO)
Crear en `/tests_temp/frontend/`:
- [Test específico 1 según tipo de proyecto]
- [Test específico 2 según tipo de proyecto]
- [Test específico N según tipo de proyecto]

**Ejecutar tests y verificar que pasen antes de continuar.**

---

## SECCIÓN 4: LIMPIEZA
### 4.1 Eliminar tests temporales
```bash
rm -rf tests_temp/
```

### 4.2 Verificación final
- [ ] Build sin errores
- [ ] Linter pasando
- [ ] Funcionalidad verificada manualmente

---

## Criterio de Completitud
Output: DONE
```

## Tests Obligatorios por Tipo de Proyecto

**REGLA CRÍTICA**: Todo plan DEBE incluir tests específicos. NO es opcional. Analizar el proyecto y definir tests concretos para cada implementación.

### Dashboard (React + FastAPI/Python)

**Backend - OBLIGATORIO:**
- Test de endpoints de datos/estadísticas (respuesta correcta, formato JSON)
- Test de filtros (parámetros válidos, rangos, combinaciones)
- Test de cálculos matemáticos/fórmulas (precisión, edge cases)
- Test de queries a DB (datos correctos, performance)

**Frontend - OBLIGATORIO:**
- Test de componentes de gráficos (renderiza con datos, estado vacío)
- Test de filtros UI (emit valores, validación inputs)
- Test de tablas/grids (paginación, ordenamiento)
- Test de integración API (loading states, error handling)

### API/Microservicio (FastAPI o Node.js Express)

**Backend - OBLIGATORIO:**
- Test de cada endpoint (GET, POST, PUT, DELETE)
- Test de validaciones de input (campos requeridos, tipos, formatos)
- Test de autenticación/autorización (tokens válidos/inválidos)
- Test de respuestas de error (404, 400, 500)
- Test de lógica de negocio (cálculos, transformaciones)

### Full Stack (Next.js + Node.js + DB)

**Backend - OBLIGATORIO:**
- Test de API routes (cada endpoint)
- Test de modelos/ORM (CRUD operations)
- Test de middleware (auth, validación)
- Test de servicios (lógica de negocio)

**Frontend - OBLIGATORIO:**
- Test de páginas (renderizado inicial, SEO meta)
- Test de formularios (validación, submit, errores)
- Test de componentes compartidos (props, estados)
- Test de hooks personalizados (estados, side effects)

### Chat/AI (Gemini/Claude + Backend)

**Backend - OBLIGATORIO:**
- Test de conexión con API del modelo
- Test de formateo de prompts/mensajes
- Test de parsing de respuestas
- Test de manejo de errores del modelo (timeouts, rate limits)
- Test de historial de conversación (contexto correcto)

**Frontend - OBLIGATORIO:**
- Test de componente de chat (mensajes, scroll)
- Test de input de usuario (envío, limpieza)
- Test de estados de carga (typing indicator)
- Test de manejo de errores UI

## Carpeta de Tests Temporales

Estructura OBLIGATORIA:
```
/tests_temp/
├── backend/
│   └── test_*.py   # Para Python
│   └── *.test.js   # Para Node.js
└── frontend/
    └── *.test.jsx  # Para React
    └── *.test.tsx  # Para TypeScript
```

**IMPORTANTE**: 
- Los tests DEBEN ejecutarse y pasar ANTES de la fase de limpieza
- La fase de limpieza SIEMPRE elimina esta carpeta al final
- Si un test falla, corregir el código, NO eliminar el test

## Generación del Comando Ralph-Loop

Al finalizar el plan, generar comando con este formato:

```bash
/ralph-loop:ralph-loop "Sigue las instrucciones del archivo [NOMBRE_ARCHIVO].md para [DESCRIPCIÓN BREVE]. Ejecutar todas las secciones en orden: [LISTAR SECCIONES]. Build sin errores. Output DONE al completar." --completion-promise "DONE" --max-iterations [N]
```

### Estimación de Max-Iterations

| Complejidad | Criterios | Iterations |
|-------------|-----------|------------|
| **Simple** | 1-2 endpoints, 1-3 componentes, sin DB | 10-15 |
| **Medio** | 3-5 endpoints, 4-8 componentes, cambios DB menores | 25-35 |
| **Complejo** | 6+ endpoints, 9+ componentes, migraciones DB, integraciones | 40-50 |

## Ejemplo de Output Completo

Ver [references/ejemplo_plan.md](references/ejemplo_plan.md) para un ejemplo real de plan generado.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gfxjef) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

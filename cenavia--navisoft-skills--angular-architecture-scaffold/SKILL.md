---
name: angular-architecture-scaffold
description: | Use when this capability is needed.
metadata:
  author: cenavia
---

# Angular Architecture Scaffold

Genera un esqueleto de proyecto Angular estrictamente alineado con el documento de arquitectura proporcionado. Interpretar el documento de forma literal.

## Obligatorio: nombre del proyecto

Antes de generar cualquier archivo, **solicitar al usuario el nombre del proyecto**. Si no lo ha indicado, preguntar explícitamente. No asumir un nombre por defecto. Usar ese nombre para:

- Carpeta raíz del proyecto
- `name` en `package.json`
- Comando `ng new` cuando se genere el proyecto (o nombre de la carpeta si se crea la estructura manualmente)

## Flujo de trabajo

1. **Obtener nombre del proyecto** (obligatorio). Si no lo ha indicado el usuario, preguntar.
2. **Generar scaffold con script** (recomendado, ahorra tokens): ejecutar el script del skill que copia el template y reemplaza placeholders. No generar archivos a mano salvo que el usuario aporte un documento de arquitectura distinto que exija otra estructura.
3. **Si el usuario aporta documento de arquitectura distinto**: cargar y analizar el documento; si el template por defecto no lo cumple, generar o adaptar archivos según el doc y [references/architecture.md](references/architecture.md) / [references/code-snippets.md](references/code-snippets.md).

### Generar con script (opción por defecto)

Desde la raíz del skill (o con la ruta absoluta al script):

```bash
python3 scripts/generate_angular_project.py <nombre-proyecto> [descripción] [directorio-salida]
```

- **nombre-proyecto** (obligatorio): nombre en kebab-case (ej. `mi-app`).
- **descripción** (opcional): breve descripción para `package.json` y README.
- **directorio-salida** (opcional): dónde crear el proyecto (por defecto: directorio actual).

Ejemplo:

```bash
python3 .claude/skills/angular-architecture-scaffold/scripts/generate_angular_project.py mi-app "Mi aplicación Angular" ./projects
```

El script copia [assets/angular-template/](assets/angular-template/) y reemplaza `{{PROJECT_NAME}}` y `{{PROJECT_DESCRIPTION}}` en `package.json`, `angular.json`, `README.md`, `src/index.html` y `src/styles.scss`. El README generado ya documenta referencias arquitectónicas, convenciones e instrucciones para extender (no hace falta que el agente lo redacte).

**Qué incluye el template**: estructura `features/home/`, `features/shared/`, `core/services/`, `core/guards/`, `core/interceptors/`; `app.ts`, `app.config.ts` (zoneless), `routes.ts` con lazy de home; componente `home.ts` (standalone, OnPush); servicio `core/services/api.ts`; guard funcional `core/guards/auth.ts`; `angular.json` sin Zone.js; `tsconfig.json` con aliases `@core/*`, `@features/*`, `@shared/*`.

## Estructura de proyecto a generar

Respetar la estructura definida en el documento. Si el documento sigue el patrón "Scope Rule" (features por alcance), usar:

```
<nombre-proyecto>/
├── src/
│   └── app/
│       ├── features/
│       │   ├── [feature-name]/
│       │   │   ├── [feature-name].ts          # Componente principal (mismo nombre que la carpeta)
│       │   │   ├── components/                 # Solo uso de esta feature
│       │   │   ├── services/
│       │   │   └── models/
│       │   └── shared/                         # Solo para uso en 2+ features
│       │       ├── components/
│       │       ├── services/
│       │       └── pipes/
│       ├── core/                               # Singletons de aplicación
│       │   ├── services/
│       │   ├── interceptors/
│       │   └── guards/
│       ├── app.ts
│       ├── app.config.ts
│       ├── routes.ts
│       └── main.ts
├── src/assets/
├── angular.json
├── tsconfig.json
├── package.json
├── .gitignore
└── README.md
```

- **Scope Rule**: un componente vive en `features/[feature]/components/` si solo lo usa esa feature; en `features/shared/components/` si lo usan dos o más features.
- **core/**: servicios, interceptores y guards de alcance global (singletons).
- No usar carpeta `modules/` con NgModule; la arquitectura es standalone con `features/`.

## Convenciones de nombres (REQUIRED)

El documento de arquitectura puede exigir **sin sufijos** en nombres de archivo. Respetar literalmente:

| Tipo        | Correcto           | Incorrecto                 |
|------------|--------------------|----------------------------|
| Componente | `user-profile.ts`  | `user-profile.component.ts`|
| Servicio   | `cart.ts`          | `cart.service.ts`          |
| Modelo     | `user.ts`          | `user.model.ts`            |

La carpeta indica el tipo; no duplicar en el nombre del archivo. Aplicar solo si el documento de arquitectura lo especifica.

## Archivos de configuración

- **package.json**: Angular en versión indicada en el doc; eliminar `zone.js` si la arquitectura es zoneless.
- **angular.json**: `styles` (p. ej. `scss` si el doc dice `--style=scss`); en `polyfills` quitar `zone.js` y `zone.js/testing` si es zoneless.
- **tsconfig.json**: `strict: true`; `paths`/aliases si el documento los define (p. ej. `@core/*`, `@features/*`, `@shared/*`).
- **app.config.ts**: si el documento exige zoneless, incluir `provideZonelessChangeDetection()` en `bootstrapApplication`.
- **main.ts**: `bootstrapApplication(AppComponent, appConfig)`.

## Patrones de código (cuando el documento lo indique)

Cargar [references/architecture.md](references/architecture.md) para detalles. Para ejemplos de archivos base (main.ts, app.config.ts, routes, componente, servicio, guard, modelo), ver [references/code-snippets.md](references/code-snippets.md). Resumen:

- **Standalone**: componentes sin `standalone: false`; no declarar `standalone: true` explícitamente si es el valor por defecto.
- **Input/Output**: usar `input()`, `input.required()`, `output()`, `model()`; no usar decoradores `@Input()`/`@Output()`.
- **Estado**: signals (`signal()`, `computed()`); no lifecycle hooks (`ngOnInit`, `ngOnChanges`, `ngOnDestroy`); usar `effect()` para reaccionar a inputs o efectos secundarios; `DestroyRef` para limpieza.
- **Inyección**: `inject()` en lugar de inyección por constructor.
- **Templates**: control flow nativo `@if`, `@for`, `@switch`; no `*ngIf`/`*ngFor`.
- **Orden en clase**: (1) dependencias inyectadas, (2) inputs/outputs, (3) estado interno, (4) computed, (5) métodos. Miembros solo para template: `protected`; inputs/outputs/queries: `readonly`.

## Rutas y lazy loading

- **routes.ts** (o equivalente): rutas con `loadComponent` o `loadChildren` para lazy loading según el documento.
- Ejemplo de ruta lazy: `loadComponent: () => import('./features/admin/admin').then(c => c.AdminComponent)`.
- Guards funcionales si el doc los menciona: `ng g g core/guards/auth --functional`.

## Comandos de generación (referencia del documento)

Si el documento incluye comandos, respetarlos:

```bash
ng new <nombre-proyecto> --style=scss --ssr=false
ng g c features/products/components/product-card --flat
ng g s features/products/services/product --flat
ng g g core/guards/auth --functional
```

El scaffold puede generarse creando archivos manualmente o indicando estos comandos en el README.

## README del proyecto

Generar un **README.md** en la raíz del proyecto que documente:

### 1. Referencias arquitectónicas clave

- Cita o enlace al documento de arquitectura usado (o indicación de que se siguió la arquitectura “Scope Rule / gentleman-programming”).
- Stack: versión de Angular, TypeScript estricto, signals, zoneless (si aplica), estilo (SCSS), SSR sí/no.
- Estructura de carpetas: `features/`, `core/`, `shared/` y criterio (Scope Rule: un componente en feature vs shared según número de features que lo usan).

### 2. Reglas, convenciones y buenas prácticas

- **Scope Rule**: dónde colocar componentes (feature vs shared).
- **Nombres de archivo**: sin sufijos `.component`, `.service`, `.model`; la carpeta define el tipo.
- **Style guide**: `inject()` sobre constructor; `class`/`style` bindings sobre `ngClass`/`ngStyle`; `protected` para miembros solo de template; `readonly` para inputs/outputs/queries; nombres de handlers por acción (`saveUser`) no por evento (`handleClick`); un concepto por archivo.
- **Estado y ciclo de vida**: signals para estado; `effect()`/`computed()`; no lifecycle hooks; `DestroyRef` para limpieza.
- **Forms**: según documento (Signal Forms experimentales vs Reactive Forms con `fb.nonNullable.group()`).
- **Rendimiento**: `NgOptimizedImage` para imágenes; `@defer` para carga diferida; rutas lazy; SSR/hydration si el doc lo indica.

### 3. Instrucciones para extender y mantener

- Cómo añadir una nueva feature (carpeta bajo `features/`, componente principal con el nombre de la feature, `components/`, `services/`, `models/`).
- Cómo añadir componentes compartidos (solo si 2+ features los usan) en `features/shared/`.
- Cómo añadir servicios/guards/interceptores globales en `core/`.
- Comandos útiles: `ng generate`, `ng build`, `ng serve`, `ng test`.
- Cómo mantener coherencia con la arquitectura: revisar Scope Rule antes de crear archivos; respetar naming y patrones (signals, inject, control flow).

El README debe ser Markdown válido y autocontenido para que cualquier desarrollador pueda seguir la arquitectura sin el documento original.

## Validación

- No introducir decisiones no explícitas (p. ej. módulos NgModule si el doc prescribe solo standalone; sufijos en nombres si el doc los prohíbe).
- Cada archivo generado debe ser coherente con el doc: imports, sintaxis (signals, inject, control flow), estructura de carpetas.
- Si se genera un proyecto con `ng new`, incluir en el README que se puede ejecutar con `npm install` y `ng serve` (o `npm run start`).

## Resumen de decisiones (arquitectura tipo context-angular)

| Área | Regla |
|------|--------|
| Estructura | `src/app/features/[feature]/`, `core/`, `shared/`; componente principal = `[feature-name].ts` |
| Scope | 1 feature → `features/[feature]/components/`; 2+ → `features/shared/components/` |
| Naming | Sin sufijos: `user-profile.ts`, `cart.ts`, `user.ts` |
| Componentes | Standalone, OnPush, `inject()`, `input()`/`output()`/`model()` |
| Estado | Signals; `effect()`/`computed()`; no `ngOnInit`/`ngOnChanges`/`ngOnDestroy` |
| Templates | `@if`, `@for`, `@switch`; no `*ngIf`/`*ngFor` |
| Zoneless | `provideZonelessChangeDetection()`; sin Zone.js en polyfills |
| Forms | Según doc: Signal Forms (exp.) o Reactive con `nonNullable.group()` |
| Imágenes | `NgOptimizedImage`, `ngSrc`, width/height o `fill` |
| Rutas | Lazy con `loadComponent`/`loadChildren` |

Si el usuario aporta un documento distinto, reemplazar este resumen por las decisiones extraídas de ese documento y generar el scaffold en consecuencia.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cenavia) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

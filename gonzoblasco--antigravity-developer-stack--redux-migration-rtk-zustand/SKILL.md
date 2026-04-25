---
name: redux-migration-rtk-zustand
description: Guía experta para refactorización incremental de Redux clásico a Redux Toolkit (RTK), y posteriormente de RTK a Zustand. Cubre patrones de migración, detección de código legacy, codemods, y verificación sin romper la aplicación. Usar cuando el usuario mencione "migrar redux", "redux toolkit", "rtk migration", "zustand migration", "refactorizar redux", "modernizar estado", o cuando se detecte código Redux legacy en el proyecto. Use when this capability is needed.
metadata:
  author: gonzoblasco
---

# Redux Migration: Redux → RTK → Zustand

> Migración incremental y segura de Redux clásico a soluciones modernas de state management.

---

## Quick Start

### 1. Detectar Estado Actual

```bash
# Ejecutar script de diagnóstico
./scripts/detect_redux_legacy.sh ./src
```

El script identifica:

- Redux Clásico (createStore, switch/case reducers)
- Redux Toolkit (createSlice, configureStore)
- Zustand (stores existentes)

### 2. Elegir Ruta de Migración

```
Redux Clásico → Redux Toolkit (RTK) → Zustand
```

| Paso | Tecnología    | Objetivo                                   |
| ---- | ------------- | ------------------------------------------ |
| 1    | Redux Toolkit | Eliminar boilerplate, inmutabilidad gratis |
| 2    | RTK Query     | Data fetching integrado (opcional)         |
| 3    | Zustand       | Simplicidad máxima, sin providers          |

### 3. Migrar Incrementalmente

> [!CAUTION]
> **NUNCA migrar todo el store de una vez.** Migrar **slice por slice**.

**Orden recomendado:**

1. Identificar slice menos crítico (ej: UI, preferences)
2. Migrar a RTK/Zustand
3. Verificar comportamiento + tests
4. Repetir con siguiente slice

---

## Fase 1: Redux → RTK (Resumen)

**Ejemplo rápido de Store:**

```typescript
// ❌ ANTES
import { createStore, combineReducers, applyMiddleware } from "redux";
const store = createStore(rootReducer, applyMiddleware(thunk));

// ✅ DESPUÉS
import { configureStore } from "@reduxjs/toolkit";
const store = configureStore({
  reducer: { users: usersReducer, products: productsReducer },
  // thunk incluido, devtools automático
});
```

**Ejemplo rápido de Slice:**

```typescript
// ❌ ANTES: types.js + actions.js + reducer.js (89 líneas)

// ✅ DESPUÉS: usersSlice.ts (35 líneas)
const usersSlice = createSlice({
  name: "users",
  initialState: { users: [], loading: false },
  reducers: {
    addUser: (state, action) => {
      state.users.push(action.payload);
    },
    removeUser: (state, action) => {
      state.users = state.users.filter((u) => u.id !== action.payload);
    },
  },
});
export const { addUser, removeUser } = usersSlice.actions;
```

📖 **Guía completa:** [references/rtk-migration.md](references/rtk-migration.md)

---

## Fase 2: RTK → Zustand (Resumen)

**¿Por qué migrar?**

| Aspecto     | RTK       | Zustand      |
| ----------- | --------- | ------------ |
| Bundle size | ~11kb     | ~1.2kb       |
| Provider    | Requerido | No necesario |
| Boilerplate | Reducido  | Mínimo       |
| TypeScript  | Bueno     | Excelente    |

**Ejemplo rápido:**

```typescript
// ❌ ANTES (RTK + Provider)
const store = configureStore({ reducer: { users: usersReducer } });
<Provider store={store}><App /></Provider>

// ✅ DESPUÉS (Zustand, sin Provider)
export const useUsersStore = create<UsersStore>()((set) => ({
  users: [],
  addUser: (user) => set((s) => ({ users: [...s.users, user] })),
  removeUser: (id) => set((s) => ({ users: s.users.filter(u => u.id !== id) })),
}));

// En componente
const { users, addUser } = useUsersStore();
```

📖 **Guía completa:** [references/zustand-migration.md](references/zustand-migration.md)

---

## Checklist de Migración

### Fase 1: Redux → RTK

- [ ] `npm install @reduxjs/toolkit`
- [ ] Convertir store con `configureStore()`
- [ ] Por cada slice:
  - [ ] Crear con `createSlice()`
  - [ ] Migrar thunks a `createAsyncThunk()`
  - [ ] Actualizar imports
  - [ ] Ejecutar tests
- [ ] Eliminar dependencias legacy (redux-thunk manual, etc.)

### Fase 2: RTK → Zustand

- [ ] `npm install zustand`
- [ ] Por cada slice:
  - [ ] Crear store Zustand equivalente
  - [ ] Actualizar componentes (eliminar useDispatch/useSelector)
  - [ ] Ejecutar tests
- [ ] Eliminar `<Provider>` cuando todo migrado
- [ ] `npm uninstall @reduxjs/toolkit react-redux`

---

## Rollback Strategy

Mantener compatibilidad durante la migración:

```typescript
// bridge.ts - Hook de transición
export function useUsers() {
  const USE_ZUSTAND = process.env.NEXT_PUBLIC_USE_ZUSTAND === "true";

  const reduxUsers = useSelector((s) => s.users.users);
  const zustandUsers = useUsersStore((s) => s.users);

  return USE_ZUSTAND ? zustandUsers : reduxUsers;
}
```

---

## Constraints

> [!WARNING]
> **Reglas inquebrantables:**

- **NUNCA** migrar más de un slice a la vez
- **SIEMPRE** verificar tests después de cada slice migrado
- **SIEMPRE** mantener ambos stores funcionando durante la transición
- **NO** eliminar Redux hasta verificar que Zustand funciona completamente
- **DOCUMENTAR** el mapeo de actions/reducers para facilitar debugging
- **BACKUP** del proyecto antes de cada fase de migración

---

## Referencias

| Documento                                                 | Contenido                    |
| --------------------------------------------------------- | ---------------------------- |
| [rtk-migration.md](references/rtk-migration.md)           | Guía detallada Redux → RTK   |
| [zustand-migration.md](references/zustand-migration.md)   | Guía detallada RTK → Zustand |
| [detection-patterns.md](references/detection-patterns.md) | Comandos grep de detección   |

## Ejemplos

| Ejemplo                                                     | Descripción                        |
| ----------------------------------------------------------- | ---------------------------------- |
| [EJEMPLO_USO.md](examples/EJEMPLO_USO.md)                   | Escenarios de activación del skill |
| [real-world-migration.md](examples/real-world-migration.md) | Caso real e-commerce con timeline  |

## Scripts

```bash
# Detectar código Redux legacy
./scripts/detect_redux_legacy.sh [directorio]
```

---

> **Recuerda**: La migración segura es incremental. Cada slice debe verificarse antes de continuar con el siguiente.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gonzoblasco) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

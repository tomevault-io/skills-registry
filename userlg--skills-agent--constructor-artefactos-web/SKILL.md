---
name: constructor-artefactos-web
description: Suite de herramientas y protocolos para construir aplicaciones web modernas (artifacts) usando React, Vite, Tailwind CSS y Shadcn/ui. Use when this capability is needed.
metadata:
  author: userlg
---

# Constructor de Artefactos Web

Este agente es un arquitecto frontend especializado en levantar aplicaciones web modernas desde cero. No usa scripts mágicos, usa estándares.

## 💀 MANDATO GLOBAL (Prime Directives)

1.  **Personalidad**: Activa **[personalidad-sarcasmo-negro](file:///d:/Projects/AI/Skill%20Agents/.agent/skills/personalidad-sarcasmo-negro/SKILL.md)**.
2.  **Bitácora**: Registra la creación del artefacto en `ACTIVITY_LOG.md`.
3.  **Optimización**: Revisa tu plan con **[optimizador-prompts-maestro](file:///d:/Projects/AI/Skill%20Agents/.agent/skills/optimizador-prompts-maestro/SKILL.md)**.

## Stack Tecnológico (The Golden Stack)

- **Framework**: React 18 + TypeScript (via Vite).
- **Styling**: Tailwind CSS 3.4+ (Standard).
- **Components**: Shadcn/ui (Radix UI primitives).
- **Bundling**: Vite (Native).

## Protocolo de Construcción Manual

A diferencia de otros agentes que dependen de scripts `init.sh`, tú sabes usar la terminal.

### Paso 1: Inicialización (Scaffolding)

```bash
npm create vite@latest nombre-proyecto -- --template react-ts
cd nombre-proyecto
npm install
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

### Paso 2: Configuración (Tailwind & Paths)

1.  Edita `tsconfig.json` para soportar alias (`@/`).
2.  Edita `vite.config.ts` para resolver alias.
3.  Configura `tailwind.config.js` con los paths de contenido y el plugin de `tailwindcss-animate`.

### Paso 3: Instalación de Componentes (Shadcn/ui)

```bash
npx shadcn-ui@latest init
# Sigue el wizard interactivo con defaults sensatos (Slate, CSS Variables).
```

### Paso 4: Desarrollo

Build the thing. Usa componentes modulares (`src/components/ui/...`).

### Paso 5: Entrega

Si el usuario necesita un solo archivo (Artifact Mode), usa herramientas de inlining, pero prefiere un repositorio limpio.

## Anti-Patterns (AI Slop)

- ❌ Gradientes púrpuras genéricos.
- ❌ Layouts centrados excesivos.
- ❌ Bordes redondeados uniformes "default".
- ❌ Uso de "Inter" font sin personalidad.

## Reference

- **Shadcn/ui**: https://ui.shadcn.com/docs
- **Vite**: https://vitejs.dev/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/userlg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

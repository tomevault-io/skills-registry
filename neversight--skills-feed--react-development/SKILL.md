---
name: react-development
description: Habilidad para desarrollar aplicaciones web modernas usando React, aplicando buenas prácticas, patrones de diseño, testing, TypeScript y frameworks del ecosistema. Use when this capability is needed.
metadata:
  author: neversight
---

## Descripción técnica

React es una biblioteca de JavaScript basada en componentes para construir interfaces de usuario. El enfoque moderno prioriza **componentes funcionales** y **Hooks**, evitando clases.

### Buenas prácticas clave

- **Estructura de carpetas**
src/
components/
hooks/
pages/
services/
tests/

- **Convenciones**
- Componentes: `PascalCase`
- Hooks personalizados: `useSomething`
- **Gestión de estado**
- Context API para estado global simple
- Redux / Zustand para lógica compleja
- **Rendimiento**
- Code splitting con `React.lazy` y `Suspense`
- **Estilos**
- Tailwind CSS, Emotion o Styled Components
- **Testing**
- Tests de componentes con React Testing Library
- **SSR / SEO**
- Next.js para aplicaciones productivas

React se integra comúnmente con pipelines de CI/CD para asegurar calidad continua antes del despliegue.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

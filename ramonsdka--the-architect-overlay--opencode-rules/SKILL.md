---
name: opencode-rules
description: > Use when this capability is needed.
metadata:
  author: RamonsDka
---

## Filosofía Opencode

- **Scream Architecture**: Los nombres de archivos y carpetas deben gritar su intención.
- **Context Overfetching Prevention**: Cargar solo las reglas necesarias mediante skills modulares.
- **AI-Agent Synergy**: El humano dirige la arquitectura, la IA ejecuta con precisión técnica.

## Patrones Arquitectónicos (Destilados)

- **Independent Operations**: Invocar múltiples herramientas en paralelo siempre que sea posible (Lovable/Windsurf pattern).
- **Research First**: Nunca adivinar contenido de archivos o estructura; usar herramientas de búsqueda antes de editar (Cursor/Devin pattern).
- **Minimal Invasive Edits**: Preferir reemplazos exactos (`str_replace`) sobre reescrituras completas para mantener el historial limpio.
- **Root Cause Debugging**: No arreglar síntomas; identificar la raíz mediante logs y aislamiento de tests antes de tocar código.

## Reglas de Nombrado y Estructura

- **Semantic Naming**: Nombres de variables y funciones que expliquen el "qué" y el "por qué" sin necesidad de comentarios.
- **Design Tokens**: Centralizar estilos en variables (HSL preferido) y evitar clases ad-hoc (Lovable pattern).
- **Atomic Components**: Crear archivos pequeños y enfocados; evitar componentes monolíticos.

## Gestión de Estado y Datos

- **Server Components (React)**: Usar Server Components para fetching de datos; evitar `useEffect` para carga inicial (Cursor rule).
- **Truthful Data**: Tratar los datos del cliente y el código como sensibles; nunca persistir secretos en el repo (Devin rule).

## Comandos Críticos

```bash
# Validar estructura de skills
ls skills/opencode

# Registrar decisiones en memoria
# mem_save title: "..." content: "..."
```

## Recursos

- **Skills**: Ver `skills/` para módulos específicos por tecnología.
- **Prompts**: Referencia original en `temp_prompts_repo/` (temporal).

---
> Source: [RamonsDka/the-architect-overlay](https://github.com/RamonsDka/the-architect-overlay) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->

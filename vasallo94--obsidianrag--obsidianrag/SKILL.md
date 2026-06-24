---
name: obsidian-plugin
description: > Use when this capability is needed.
metadata:
  author: Vasallo94
---

# Obsidian Plugin Development

## Cuándo usar esta skill
- Cuando modifiques la interfaz de usuario en Obsidian (`plugin/src`).
- Cuando cambies la lógica de comunicación con el servidor Python.
- Cuando actualices estilos CSS (`styles.css`).

## Cómo usar esta skill

### 1. Estructura (TypeScript)
- `main.ts`: Entry point, carga settings y registra vistas.
- `chat-view.ts`: Vista del chat (UI).
- `server-manager.ts`: Gestiona el subproceso del servidor Python (`spawn`).
- `api-client.ts`: Cliente HTTP para hablar con `http://localhost:port`.

### 2. Streaming (SSE)
El plugin soporta Server-Sent Events para respuestas progresivas.
- Usa `parseSSEStream` en el cliente para manejar eventos como `token`, `sources`, `done`.

### 3. Desarrollo
```bash
cd plugin
npm install
npm run dev  # Watch mode
```

### 4. Mejores Prácticas
- **UI**: Usa componentes nativos de Obsidian donde sea posible.
- **Estilos**: Usa variables CSS de Obsidian (`--background-primary`, etc.) para soporte de temas.
- **Procesos**: Asegúrate de matar el proceso de Python al descargar el plugin (`onunload`).

---
> Source: [Vasallo94/ObsidianRAG](https://github.com/Vasallo94/ObsidianRAG) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->

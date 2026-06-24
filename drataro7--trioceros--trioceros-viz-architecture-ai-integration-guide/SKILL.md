---
name: trioceros-viz-architecture-ai-integration-guide
description: Documentation for AI Agents (like Antigravity/Cursor/Windsurf) to understand how to manipulate the Trioceros-Viz 3D engine, add new Function Calling tools, and pass data to WebGL/WASM buffers. Use when this capability is needed.
metadata:
  author: drataro7
---

# 🦖 Trioceros-Viz (morphic-viz) Agent Skill Guide

Welcome, AI Agent! You are working inside the `trioceros-viz` repository. This is an ultra-high-performance 3D Data Visualization Engine using Next.js, React Three Fiber, WebGL, and Rust (WASM) geometry generation. 

## 1. 🏗️ System Architecture

*   **Frontend**: Next.js App Router (`src/app/page.tsx`).
*   **3D Engine**: `src/components/Canvas3DComponent.tsx` (Handles instanced BufferGeometries, Shaders, GPU Animations).
*   **Data Generation (WASM)**: Compiled Rust (`src/wasm/pkg`) builds massive point cloud arrays (up to 1,000,000 points) blazingly fast.
*   **AI Integration**: Vercel AI SDK (`src/app/api/chat/route.ts` & `useChat` in frontend).

## 2. 🤖 How to Add AI Functionalities (God Tools)

To allow the user's AI to manipulate the 3D Engine, you must use **Vercel AI SDK Function Calling**.

**Step 1. Define the Tool (Backend)**:
In `src/app/api/chat/route.ts`, add a Zod-validated tool to the `tools` object.
```typescript
import { z } from 'zod';
import { tool } from 'ai';

export const POST = ...
  const result = streamText({
    tools: {
      setMode: tool({...}),
      // Add your new tool here e.g.:
      changeTheme: tool({
        description: 'Change the cyber topology color theme based on chat context.',
        parameters: z.object({ theme: z.enum(['cyberpunk', 'matrix', 'gold']) })
      })
    }
  });
```

**Step 2. Handle the Tool (Frontend)**:
In `src/app/page.tsx`, intercept the tool inside `useChat`:
```tsx
const { messages, append } = useChat({
  onToolCall({ toolCall }: any) {
    if (toolCall.toolName === 'changeTheme') {
      const { theme } = toolCall.args;
      setTopologyTheme(theme);
      return { success: true };
    }
  }
});
```

## 3. 🌠 Modifying the 3D Engine (Shaders & Rendering)

If you are asked to "Change how the planets orbit" or "Make the particles glow", you MUST edit `src/components/Canvas3DComponent.tsx`.

*   **Rule of Thumb**: Do NOT try to modify Javascript `setInterval` or `requestAnimationFrame` for positioning 100,000 points. It will crash the browser.
*   **Use WebGL**: Write algorithms inside `vertexShader`. `uProgress` controls morphing interpolation, `uTime` controls continuous animations, and `uDataMode` differentiates behaviors per mode.

## 4. 🗄️ Pushing Custom Data to WASM
When `dataMode === "custom"`, the Engine reads from the `customData` state prop.
The raw object structure pushed into `customData` must match what `PointCloudRenderer::parse_json` expects from Rust:
```json
[
  { "x": 0.0, "y": 10.0, "z": -5.0, "value": 100, "color_r": 1.0, "color_g": 0.0, "color_b": 0.0 },
  ...
]
```
Do NOT pass raw deeply nested objects. Map them into the generic `{x, y, z, r, g, b}` format.

Enjoy building the next 10-Trillion-Dollar startup UI!

---
> Source: [drataro7/trioceros](https://github.com/drataro7/trioceros) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->

---
name: streamdown
description: >- Use when this capability is needed.
metadata:
  author: ojpbarbosa
---

# Streamdown

Streaming-optimized React Markdown renderer. Drop-in replacement for `react-markdown` with built-in streaming support, security, and interactive controls.

## Quick Setup

### 1. Install

```bash
npm install streamdown
```

Optional plugins (install only what's needed):
```bash
npm install @streamdown/code @streamdown/mermaid @streamdown/math @streamdown/cjk
```

### 2. Configure Tailwind CSS (Required)

**This is the most commonly missed step.** Streamdown uses Tailwind for styling and the dist files must be scanned.

**Tailwind v4** — add to `globals.css`:
```css
@source "../node_modules/streamdown/dist/*.js";
```

**Tailwind v3** — add to `tailwind.config.js`:
```js
module.exports = {
  content: [
    "./app/**/*.{js,ts,jsx,tsx,mdx}",
    "./node_modules/streamdown/dist/*.js",
  ],
};
```

### 3. Basic Usage

```tsx
import { Streamdown } from 'streamdown';

<Streamdown>{markdown}</Streamdown>
```

### 4. With AI Streaming (Vercel AI SDK)

```tsx
'use client';
import { useChat } from '@ai-sdk/react';
import { TextStreamChatTransport } from 'ai';
import { Streamdown } from 'streamdown';
import { code } from '@streamdown/code';
import { useState, useMemo } from 'react';

export default function Chat() {
  const [input, setInput] = useState('');
  const transport = useMemo(
    () => new TextStreamChatTransport({ api: '/api/chat' }),
    []
  );
  const { messages, sendMessage, status } = useChat({ transport });
  const isLoading = status === 'streaming' || status === 'submitted';

  const getTextContent = (msg: (typeof messages)[number]) =>
    msg.parts
      .filter((p): p is { type: 'text'; text: string } => p.type === 'text')
      .map((p) => p.text)
      .join('');

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    if (!input.trim()) return;
    sendMessage({ text: input });
    setInput('');
  };

  return (
    <>
      {messages.map((msg, i) => (
        <Streamdown
          key={msg.id}
          plugins={{ code }}
          caret="block"
          isAnimating={isLoading && i === messages.length - 1 && msg.role === 'assistant'}
        >
          {getTextContent(msg)}
        </Streamdown>
      ))}
      <form onSubmit={handleSubmit}>
        <input value={input} onChange={(e) => setInput(e.target.value)} disabled={isLoading} />
        <button type="submit">Send</button>
      </form>
    </>
  );
}
```

**Server route** (`app/api/chat/route.ts`): Messages from `useChat` use `parts` (not `content`). Extract text before passing to `streamText`:

```ts
import { streamText } from 'ai';

export async function POST(req: Request) {
  const { messages: rawMessages } = await req.json();

  // useChat sends messages with `parts`, but streamText expects `content`
  const messages = rawMessages.map((msg: any) => ({
    role: msg.role,
    content: msg.content ?? msg.parts?.filter((p: any) => p.type === 'text').map((p: any) => p.text).join('') ?? '',
  }));

  const result = streamText({ model: yourModel, messages });
  return result.toTextStreamResponse();
}
```

### 5. Static Mode (Blogs, Docs)

```tsx
<Streamdown mode="static" plugins={{ code }}>
  {content}
</Streamdown>
```

## Key Props

| Prop | Type | Default | Purpose |
|------|------|---------|---------|
| `children` | `string` | — | Markdown content |
| `mode` | `"streaming" \| "static"` | `"streaming"` | Rendering mode |
| `plugins` | `{ code?, mermaid?, math?, cjk? }` | — | Feature plugins |
| `isAnimating` | `boolean` | `false` | Streaming indicator |
| `caret` | `"block" \| "circle"` | — | Cursor style |
| `components` | `Components` | — | Custom element overrides |
| `controls` | `boolean \| object` | `true` | Interactive buttons |
| `linkSafety` | `LinkSafetyConfig` | `{ enabled: true }` | Link confirmation modal |
| `shikiTheme` | `[light, dark]` | `['github-light', 'github-dark']` | Code themes |
| `className` | `string` | — | Container class |
| `allowedElements` | `string[]` | all | Tag names to allow |
| `disallowedElements` | `string[]` | `[]` | Tag names to disallow |
| `allowElement` | `AllowElement` | — | Custom element filter |
| `unwrapDisallowed` | `boolean` | `false` | Keep children of disallowed elements |
| `skipHtml` | `boolean` | `false` | Ignore raw HTML |
| `urlTransform` | `UrlTransform` | `defaultUrlTransform` | Transform/sanitize URLs |

For full API reference, see [references/api.md](references/api.md).

## Plugin Quick Reference

| Plugin | Package | Purpose |
|--------|---------|---------|
| Code | `@streamdown/code` | Syntax highlighting (Shiki, 200+ languages) |
| Mermaid | `@streamdown/mermaid` | Diagrams (flowcharts, sequence, etc.) |
| Math | `@streamdown/math` | LaTeX via KaTeX (requires CSS import) |
| CJK | `@streamdown/cjk` | Chinese/Japanese/Korean text support |

**Math requires CSS:**
```tsx
import 'katex/dist/katex.min.css';
```

For plugin configuration details, see [references/plugins.md](references/plugins.md).

## References

Use these for deeper implementation details:

- **[references/api.md](references/api.md)** — Complete props, types, and interfaces
- **[references/plugins.md](references/plugins.md)** — Plugin setup, configuration, and customization
- **[references/styling.md](references/styling.md)** — CSS variables, data attributes, custom components, theme examples
- **[references/security.md](references/security.md)** — Hardening, link safety, custom HTML tags, production config
- **[references/features.md](references/features.md)** — Carets, remend, static mode, controls, GFM, memoization, troubleshooting

## Example Configurations

Copy and adapt from `assets/examples/`:

- **[basic-streaming.tsx](assets/examples/basic-streaming.tsx)** — Minimal AI chat with Vercel AI SDK
- **[with-caret.tsx](assets/examples/with-caret.tsx)** — Streaming with block caret cursor
- **[full-featured.tsx](assets/examples/full-featured.tsx)** — All plugins, carets, link safety, controls
- **[static-mode.tsx](assets/examples/static-mode.tsx)** — Blog/docs rendering
- **[custom-security.tsx](assets/examples/custom-security.tsx)** — Strict security for AI content

## Common Gotchas

1. **Tailwind styles missing** — Add `@source` directive or `content` entry for `node_modules/streamdown/dist/*.js`
2. **Math not rendering** — Import `katex/dist/katex.min.css`
3. **Caret not showing** — Both `caret` prop AND `isAnimating={true}` are required
4. **Copy buttons during streaming** — Disabled automatically when `isAnimating={true}`
5. **Link safety modal appearing** — Enabled by default; disable with `linkSafety={{ enabled: false }}`
6. **Shiki warning in Next.js** — Install `shiki` explicitly, add to `transpilePackages`
7. **`allowedTags` not working** — Only works with default rehype plugins
8. **Math uses `$$` not `$`** — Single dollar is disabled by default to avoid currency conflicts

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ojpbarbosa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

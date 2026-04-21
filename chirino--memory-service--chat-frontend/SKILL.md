---
name: chat-frontend
description: Use when working on the React chat frontend. Covers architecture, components, styling, and state management.
metadata:
  author: chirino
---

# Chat Frontend Context

Location: `frontends/chat-frontend/`

## Tech Stack
- **React 19** with TypeScript, Vite 7 build tool
- **Tailwind CSS** for styling (custom theme in `tailwind.config.js`)
- **TanStack React Query** for server state
- **Radix UI** primitives for accessible components
- **OpenAPI client** auto-generated in `src/client/`

## Key Directories
```
src/
├── components/           # React components
│   ├── ui/               # Reusable primitives (button, card, toggle)
│   ├── sharing/          # Access control modals
│   ├── conversation.tsx  # Headless chat primitives (core logic)
│   ├── conversations-ui.tsx  # Styled wrappers with Tailwind
│   ├── chat-panel.tsx    # Main chat interface
│   └── chat-sidebar.tsx  # Conversation list
├── hooks/                # useSseStream, useSharing, useResumeCheck
├── lib/                  # auth.tsx, utils.ts
└── client/               # Generated OpenAPI client (DO NOT EDIT)
```

## Component Architecture

**Headless Pattern**: `conversation.tsx` provides unstyled primitives (`Conversation.Root`, `.Viewport`, `.Messages`, `.Message`, `.Input`, `.Actions`) with hooks (`useConversationContext`, `useConversationMessages`, etc.).

**Styled Wrappers**: `conversations-ui.tsx` wraps headless components with Tailwind styling.

**Main Components**:
- `App.tsx` - Root layout (sidebar + chat panel)
- `chat-panel.tsx` - Message display, input, fork UI, sharing
- `chat-sidebar.tsx` - Conversation list with search

## Styling

**Custom Tailwind Theme** (`tailwind.config.js`):
- Colors: `cream` (bg), `ink` (text), `mist` (secondary), `stone` (muted), `terracotta` (accent), `sage` (secondary accent)
- Fonts: Instrument Serif (headings), DM Sans (body), SF Mono (code)

**Utility**: `cn()` from `lib/utils.ts` merges classes with Tailwind conflict resolution.

## State Management
- **React Query**: Server state (conversations, messages, memberships)
- **Context**: Auth (`lib/auth.tsx`), conversation state (`conversation.tsx`)
- **Local**: UI-only state via `useState`

## Dev Commands
```bash
cd frontends/chat-frontend
npm run dev        # Dev server with HMR
npm run build      # Production build
npm run lint       # ESLint
npm run generate   # Regenerate OpenAPI client
```

## Common Tasks

**Add/modify component styling**: Edit in `conversations-ui.tsx` or relevant component, use Tailwind classes with `cn()`.

**Change chat behavior**: Modify `conversation.tsx` (logic) or `chat-panel.tsx` (UI integration).

**Fix z-index/layering issues**: Check `z-*` classes in Tailwind. Common layers: dropdowns (z-50), modals (z-[100]).

**After changes**: Run `npm run lint && npm run build` to verify.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chirino) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

---
name: nextjs-mantine-stack
description: | Use when this capability is needed.
metadata:
  author: ray123fa
---

# Next.js 16 + Tailwind v4 + Mantine UI v8 Stack

## Tech Stack

| Library | Version | Purpose |
|---------|---------|---------|
| Next.js | 16.x | React framework + Turbopack |
| Tailwind CSS | v4 | Utility-first CSS |
| Mantine UI | v8.3.14 | Component library |
| Zustand | v5 | State management |
| TanStack Query | v5 | Data fetching |

## Quick Start

```bash
# Create project (includes TypeScript, Tailwind, App Router, Turbopack)
npx create-next-app@latest my-app --yes && cd my-app

# Install all dependencies
npm install @mantine/core@8.3.14 @mantine/hooks@8.3.14 @mantine/form@8.3.14 @mantine/dates@8.3.14 dayjs zustand @tanstack/react-query @tanstack/react-query-devtools @tailwindcss/postcss
```

## Project Structure

```
src/
├── app/
│   ├── layout.tsx       # Root layout with providers
│   ├── page.tsx         # Home page
│   └── globals.css      # Tailwind imports
├── components/          # UI components
├── providers/
│   ├── index.tsx        # Combined providers
│   └── query-provider.tsx
├── stores/              # Zustand stores
├── hooks/               # Query hooks
├── lib/                 # Utilities
└── types/               # TypeScript types
```

## Core Configuration

### postcss.config.mjs

```javascript
const config = {
  plugins: { "@tailwindcss/postcss": {} },
};
export default config;
```

### src/app/globals.css

```css
@import "tailwindcss";

@theme {
  --color-primary: #339af0;
  --color-secondary: #7950f2;
}
```

### src/app/layout.tsx

```tsx
import "@mantine/core/styles.css";
import "@mantine/dates/styles.css";
import "./globals.css";

import { ColorSchemeScript, mantineHtmlProps } from "@mantine/core";
import { Providers } from "@/providers";

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en" {...mantineHtmlProps}>
      <head><ColorSchemeScript /></head>
      <body><Providers>{children}</Providers></body>
    </html>
  );
}
```

### src/providers/index.tsx

```tsx
"use client";
import { MantineProvider, createTheme } from "@mantine/core";
import { QueryProvider } from "./query-provider";

const theme = createTheme({ primaryColor: "blue", defaultRadius: "md" });

export function Providers({ children }: { children: React.ReactNode }) {
  return (
    <QueryProvider>
      <MantineProvider theme={theme}>{children}</MantineProvider>
    </QueryProvider>
  );
}
```

### src/providers/query-provider.tsx

```tsx
"use client";
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import { ReactQueryDevtools } from "@tanstack/react-query-devtools";
import { useState } from "react";

export function QueryProvider({ children }: { children: React.ReactNode }) {
  const [queryClient] = useState(() => new QueryClient({
    defaultOptions: { queries: { staleTime: 60 * 1000 } }
  }));
  return (
    <QueryClientProvider client={queryClient}>
      {children}
      <ReactQueryDevtools initialIsOpen={false} />
    </QueryClientProvider>
  );
}
```

## References

For detailed patterns and examples:

- **Zustand store patterns**: See [references/zustand.md](references/zustand.md)
- **TanStack Query hooks**: See [references/tanstack-query.md](references/tanstack-query.md)
- **Mantine components & forms**: See [references/mantine.md](references/mantine.md)

## Commands

```bash
npm run dev      # Development (Turbopack)
npm run build    # Production build
npm start        # Start production
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ray123fa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

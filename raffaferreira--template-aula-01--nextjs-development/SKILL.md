---
name: nextjs-development
description: Expertise in Next.js 16 App Router development with TypeScript, React 19, and Tailwind CSS. Use when working with Next.js projects, server components, client components, routing, or modern React patterns. Use when this capability is needed.
metadata:
  author: raffaferreira
---

# Next.js 16 Development Skill

Este skill fornece orientação especializada para desenvolvimento com Next.js 16 usando App Router, TypeScript e React 19.

## Quando usar esta skill

Use esta skill quando precisar:

- Criar ou modificar componentes Next.js (Server/Client Components)
- Implementar rotas usando App Router
- Configurar layouts, loading states, e error boundaries
- Trabalhar com React Server Components (RSC)
- Integrar Tailwind CSS e Bootstrap 5
- Otimizar performance e SEO
- Implementar Data Fetching patterns

## Estrutura de Diretórios

```
app/
├── layout.tsx          # Root layout (Server Component)
├── page.tsx            # Home page
├── globals.css         # Estilos globais
└── [route]/
    ├── layout.tsx      # Layout específico da rota
    ├── page.tsx        # Página da rota
    ├── loading.tsx     # Loading UI
    └── error.tsx       # Error boundary
```

## Padrões de Componentes

### Server Component (padrão)
```tsx
// app/page.tsx
export default async function Page() {
  const data = await fetch('https://api.example.com/data');
  return <div>{data.title}</div>;
}
```

### Client Component
```tsx
'use client'; // Diretiva obrigatória no topo

import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

## Melhores Práticas

1. **Use Server Components por padrão**
   - Menor bundle JavaScript no cliente
   - Acesso direto a recursos do servidor

2. **Client Components apenas quando necessário**
   - Interatividade (useState, useEffect, event handlers)
   - Hooks do navegador (useSearchParams, usePathname)
   - Bibliotecas que dependem de APIs do navegador

3. **Otimização de Imagens**
   ```tsx
   import Image from 'next/image';
   
   <Image
     src="/hero.jpg"
     alt="Hero"
     width={800}
     height={600}
     priority // Para LCP
   />
   ```

4. **Metadata para SEO**
   ```tsx
   // app/page.tsx
   export const metadata = {
     title: 'Página Inicial',
     description: 'Descrição da página',
   };
   ```

5. **Loading States**
   ```tsx
   // app/dashboard/loading.tsx
   export default function Loading() {
     return <div>Carregando...</div>;
   }
   ```

## Integração com Tailwind CSS

```tsx
// Componente com Tailwind + Bootstrap híbrido
export default function Card() {
  return (
    <div className="rounded-lg shadow-md p-4 bg-white">
      <h2 className="text-2xl font-bold mb-2">Título</h2>
      <p className="text-gray-600">Conteúdo</p>
    </div>
  );
}
```

## Data Fetching Patterns

### Fetch com Revalidação
```tsx
const res = await fetch('https://api.example.com/data', {
  next: { revalidate: 3600 } // Revalidar a cada hora
});
```

### Fetch Dinâmico (sempre atualizado)
```tsx
const res = await fetch('https://api.example.com/data', {
  cache: 'no-store'
});
```

## Configurações Importantes

### next.config.ts
```ts
import type { NextConfig } from 'next';

const nextConfig: NextConfig = {
  reactStrictMode: true,
  images: {
    domains: ['example.com'],
  },
  experimental: {
    serverActions: true,
  },
};

export default nextConfig;
```

## Comandos Úteis

```bash
npm run dev          # Dev server (http://localhost:3000)
npm run build        # Build de produção
npm run start        # Servidor de produção
npm run lint         # ESLint check
npm run type-check   # TypeScript check
```

## Referências

- [Next.js Docs](https://nextjs.org/docs)
- [React 19 Docs](https://react.dev)
- [Tailwind CSS Docs](https://tailwindcss.com/docs)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/raffaferreira) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

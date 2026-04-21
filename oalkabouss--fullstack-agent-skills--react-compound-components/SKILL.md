---
name: react-compound-components
description: React compound components pattern (safe, minimal context, ergonomic API) Use when this capability is needed.
metadata:
  author: oalkabouss
---
## What I do

Je fournis un guide pour implémenter des **Compound Components** sûrs :
- contexte minimal
- API ergonomique
- erreurs explicites

## Minimal template

```tsx
type Ctx = { id: string };
const Ctx = createContext<Ctx | null>(null);

export function Root({ id, children }: { id: string; children: ReactNode }) {
  return <Ctx.Provider value={{ id }}>{children}</Ctx.Provider>;
}

Root.Label = function Label({ children }: { children: ReactNode }) {
  const ctx = useContext(Ctx);
  if (!ctx) throw new Error('Root.Label must be used within Root');
  return <label htmlFor={ctx.id}>{children}</label>;
};
```

## When NOT to use
- Si l'état partagé devient complexe : passer à props explicites + hook.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oalkabouss) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

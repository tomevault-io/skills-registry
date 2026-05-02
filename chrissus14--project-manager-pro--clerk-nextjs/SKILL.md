---
name: clerk-nextjs
description: Clerk Authentication Skill for Next.js. Use when this capability is needed.
metadata:
  author: chrissus14
---

## Reglas generales

- Siempre usar App Router (no Pages Router)
- Middleware en src/middleware.ts
- ClerkProvider en layout raíz
- Server Components por defecto

## Estructura de autenticación

### Middleware Pattern

```typescript
import { clerkMiddleware, createRouteMatcher } from "@clerk/nextjs/server";

const isPublicRoute = createRouteMatcher(["/", "/sign-in(.*)", "/sign-up(.*)"]);

export default clerkMiddleware(async (auth, request) => {
  if (!isPublicRoute(request)) {
    await auth.protect();
  }
});
```

### Layout protegido Pattern

```typescript
import { auth } from '@clerk/nextjs/server';
import { redirect } from 'next/navigation';

export default async function ProtectedLayout({ children }) {
  const { userId } = await auth();
  if (!userId) redirect('/sign-in');
  return <>{children}</>;
}
```

### Obtener usuario actual

```typescript
// En Server Component
import { currentUser } from "@clerk/nextjs/server";
const user = await currentUser();

// En Client Component
import { useUser } from "@clerk/nextjs";
const { user } = useUser();
```

## Componentes de Clerk

- `<SignIn />` - Formulario de login
- `<SignUp />` - Formulario de registro
- `<UserButton />` - Avatar con menú
- `<UserProfile />` - Página de perfil

## Variables de entorno requeridas

```env
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=
CLERK_SECRET_KEY=
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up
```

## Mejores prácticas

1. Server Components para auth checks (más seguro)
2. Client Components solo para UI interactiva
3. Usar middleware para protección global
4. Nunca exponer CLERK_SECRET_KEY en cliente

```

```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chrissus14) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

---
name: better-auth
description: Better Auth integration patterns for authentication in TypeScript applications. Use when implementing authentication, sessions, OAuth, or user management. Use when this capability is needed.
metadata:
  author: allanninal
---

# Better Auth Integration

## When to Use This Skill

- Setting up authentication in TypeScript apps
- Implementing OAuth providers (Google, GitHub, etc.)
- Managing user sessions
- Building login/signup flows
- Handling password reset and email verification

## Setup

### Installation

```bash
npm install better-auth
# or
pnpm add better-auth
```

### Basic Configuration

```typescript
// lib/auth.ts
import { betterAuth } from 'better-auth';
import { prismaAdapter } from 'better-auth/adapters/prisma';
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

export const auth = betterAuth({
  database: prismaAdapter(prisma, {
    provider: 'postgresql', // or 'mysql', 'sqlite'
  }),
  emailAndPassword: {
    enabled: true,
    requireEmailVerification: true,
  },
  session: {
    expiresIn: 60 * 60 * 24 * 7, // 7 days
    updateAge: 60 * 60 * 24, // 1 day
  },
});
```

### Database Schema (Prisma)

```prisma
// prisma/schema.prisma
model User {
  id            String    @id @default(cuid())
  email         String    @unique
  emailVerified DateTime?
  name          String?
  image         String?
  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt

  sessions Session[]
  accounts Account[]
}

model Session {
  id        String   @id @default(cuid())
  userId    String
  expiresAt DateTime
  token     String   @unique
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)
}

model Account {
  id                String  @id @default(cuid())
  userId            String
  accountId         String
  providerId        String
  accessToken       String?
  refreshToken      String?
  accessTokenExpiresAt DateTime?
  refreshTokenExpiresAt DateTime?
  scope             String?
  idToken           String?
  createdAt         DateTime @default(now())
  updatedAt         DateTime @updatedAt

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@unique([providerId, accountId])
}

model Verification {
  id         String   @id @default(cuid())
  identifier String
  value      String
  expiresAt  DateTime
  createdAt  DateTime @default(now())
  updatedAt  DateTime @updatedAt

  @@unique([identifier, value])
}
```

## OAuth Providers

### Configure Providers

```typescript
// lib/auth.ts
import { betterAuth } from 'better-auth';

export const auth = betterAuth({
  // ... database config
  socialProviders: {
    google: {
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
    },
    github: {
      clientId: process.env.GITHUB_CLIENT_ID!,
      clientSecret: process.env.GITHUB_CLIENT_SECRET!,
    },
    discord: {
      clientId: process.env.DISCORD_CLIENT_ID!,
      clientSecret: process.env.DISCORD_CLIENT_SECRET!,
    },
  },
});
```

### OAuth Callback URLs

```
Google: http://localhost:3000/api/auth/callback/google
GitHub: http://localhost:3000/api/auth/callback/github
Discord: http://localhost:3000/api/auth/callback/discord
```

## API Routes

### Next.js App Router

```typescript
// app/api/auth/[...all]/route.ts
import { auth } from '@/lib/auth';
import { toNextJsHandler } from 'better-auth/next-js';

export const { GET, POST } = toNextJsHandler(auth.handler);
```

### Express

```typescript
// routes/auth.ts
import express from 'express';
import { auth } from '../lib/auth';
import { toNodeHandler } from 'better-auth/node';

const router = express.Router();

router.all('/api/auth/*', toNodeHandler(auth.handler));

export default router;
```

## Client Integration

### React Client

```typescript
// lib/auth-client.ts
import { createAuthClient } from 'better-auth/react';

export const authClient = createAuthClient({
  baseURL: process.env.NEXT_PUBLIC_APP_URL,
});

export const { signIn, signUp, signOut, useSession } = authClient;
```

### Usage in Components

```typescript
// components/AuthButtons.tsx
'use client';

import { signIn, signOut, useSession } from '@/lib/auth-client';

export function AuthButtons() {
  const { data: session, isPending } = useSession();

  if (isPending) {
    return <div>Loading...</div>;
  }

  if (session) {
    return (
      <div>
        <p>Welcome, {session.user.name}</p>
        <button onClick={() => signOut()}>Sign Out</button>
      </div>
    );
  }

  return (
    <div>
      <button onClick={() => signIn.social({ provider: 'google' })}>
        Sign in with Google
      </button>
      <button onClick={() => signIn.social({ provider: 'github' })}>
        Sign in with GitHub
      </button>
    </div>
  );
}
```

### Email/Password Auth

```typescript
// components/LoginForm.tsx
'use client';

import { signIn, signUp } from '@/lib/auth-client';
import { useState } from 'react';

export function LoginForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [isSignUp, setIsSignUp] = useState(false);

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();

    if (isSignUp) {
      const result = await signUp.email({
        email,
        password,
        name: email.split('@')[0],
      });

      if (result.error) {
        console.error(result.error);
      }
    } else {
      const result = await signIn.email({ email, password });

      if (result.error) {
        console.error(result.error);
      }
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email"
        required
      />
      <input
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
        placeholder="Password"
        required
      />
      <button type="submit">{isSignUp ? 'Sign Up' : 'Sign In'}</button>
      <button type="button" onClick={() => setIsSignUp(!isSignUp)}>
        {isSignUp ? 'Have an account? Sign in' : 'Need an account? Sign up'}
      </button>
    </form>
  );
}
```

## Server-Side Auth

### Get Session in Server Components

```typescript
// app/dashboard/page.tsx
import { auth } from '@/lib/auth';
import { headers } from 'next/headers';
import { redirect } from 'next/navigation';

export default async function DashboardPage() {
  const session = await auth.api.getSession({
    headers: headers(),
  });

  if (!session) {
    redirect('/login');
  }

  return (
    <div>
      <h1>Dashboard</h1>
      <p>Welcome, {session.user.name}</p>
    </div>
  );
}
```

### Middleware Protection

```typescript
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
import { auth } from '@/lib/auth';

export async function middleware(request: NextRequest) {
  const session = await auth.api.getSession({
    headers: request.headers,
  });

  if (!session && request.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  return NextResponse.next();
}

export const config = {
  matcher: ['/dashboard/:path*', '/api/protected/:path*'],
};
```

## Advanced Features

### Email Verification

```typescript
// lib/auth.ts
import { betterAuth } from 'better-auth';
import { sendVerificationEmail } from './email';

export const auth = betterAuth({
  // ... config
  emailAndPassword: {
    enabled: true,
    requireEmailVerification: true,
    sendVerificationEmail: async ({ user, url }) => {
      await sendVerificationEmail({
        to: user.email,
        subject: 'Verify your email',
        html: `<a href="${url}">Click to verify</a>`,
      });
    },
  },
});
```

### Password Reset

```typescript
// lib/auth.ts
export const auth = betterAuth({
  // ... config
  emailAndPassword: {
    enabled: true,
    sendResetPassword: async ({ user, url }) => {
      await sendEmail({
        to: user.email,
        subject: 'Reset your password',
        html: `<a href="${url}">Reset password</a>`,
      });
    },
  },
});

// Client usage
import { authClient } from '@/lib/auth-client';

await authClient.forgetPassword({ email: 'user@example.com' });
await authClient.resetPassword({ token, newPassword });
```

### Two-Factor Authentication

```typescript
// lib/auth.ts
import { betterAuth } from 'better-auth';
import { twoFactor } from 'better-auth/plugins';

export const auth = betterAuth({
  // ... config
  plugins: [
    twoFactor({
      issuer: 'My App',
    }),
  ],
});

// Client
const { twoFactor } = authClient;

// Enable 2FA
const { totpURI } = await twoFactor.enable();

// Verify 2FA
await twoFactor.verify({ code: '123456' });
```

## Best Practices

- [ ] Always use HTTPS in production
- [ ] Store secrets in environment variables
- [ ] Enable email verification for new accounts
- [ ] Implement rate limiting on auth endpoints
- [ ] Use secure session settings
- [ ] Add CSRF protection
- [ ] Log authentication events
- [ ] Implement account lockout after failed attempts

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/allanninal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

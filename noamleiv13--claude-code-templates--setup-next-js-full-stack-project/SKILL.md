---
name: setup-next-js-full-stack-project
description: > Use when this capability is needed.
metadata:
  author: noamLeiv13
---

# Setup Next.js Full Stack Project

## Pre-requisites

Ask the user for:
1. **Project name**
2. **CockroachDB connection string** (DATABASE_URL)
3. **Google OAuth client ID and secret** (from Google Cloud Console)
4. **App title** (for metadata)
5. **Gemini API key** (optional, for AI features)

Generate a NextAuth secret: `openssl rand -base64 32`

## Step 1: Create Next.js App

```bash
npx create-next-app@latest <project-name> --typescript --tailwind --app --src-dir
cd <project-name>
```

**IMPORTANT**: After creating, check `node_modules/next/dist/docs/` for any breaking changes in this Next.js version.

## Step 2: Install Dependencies

```bash
npm install prisma @prisma/client @prisma/adapter-pg pg dotenv next-auth@beta @auth/prisma-adapter
npm install -D @types/pg
```

## Step 3: Create `prisma.config.ts` (project root)

```typescript
import { config } from "dotenv";
import { defineConfig } from "prisma/config";

config({ path: ".env.local" });

export default defineConfig({
  schema: "prisma/schema.prisma",
  migrations: {
    path: "prisma/migrations",
  },
  datasource: {
    url: process.env["DATABASE_URL"],
  },
});
```

## Step 4: Create `prisma/schema.prisma`

```prisma
datasource db {
  provider = "cockroachdb"
}

generator client {
  provider = "prisma-client-js"
  output   = "../src/generated/prisma"
}

// NextAuth required models
model Account {
  id                String  @id @default(cuid())
  userId            String
  type              String
  provider          String
  providerAccountId String
  refresh_token     String?
  access_token      String?
  expires_at        Int?
  token_type        String?
  scope             String?
  id_token          String?
  session_state     String?

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@unique([provider, providerAccountId])
}

model Session {
  id           String   @id @default(cuid())
  sessionToken String   @unique
  userId       String
  expires      DateTime
  user         User     @relation(fields: [userId], references: [id], onDelete: Cascade)
}

model User {
  id            String    @id @default(cuid())
  name          String?
  email         String?   @unique
  emailVerified DateTime?
  image         String?
  accounts      Account[]
  sessions      Session[]
  createdAt     DateTime  @default(now())
}

model VerificationToken {
  identifier String
  token      String   @unique
  expires    DateTime

  @@unique([identifier, token])
}
```

Add application-specific models below the NextAuth models based on the user's requirements.

## Step 5: Create `src/lib/prisma.ts`

```typescript
import { PrismaClient } from "@/generated/prisma";
import { PrismaPg } from "@prisma/adapter-pg";

const globalForPrisma = globalThis as unknown as { prisma: PrismaClient };

function createPrismaClient() {
  const adapter = new PrismaPg(process.env.DATABASE_URL!);
  return new PrismaClient({ adapter });
}

export const prisma = globalForPrisma.prisma || createPrismaClient();

if (process.env.NODE_ENV !== "production") globalForPrisma.prisma = prisma;
```

## Step 6: Create `src/lib/auth.ts`

```typescript
import NextAuth from "next-auth";
import Google from "next-auth/providers/google";
import { PrismaAdapter } from "@auth/prisma-adapter";
import { prisma } from "./prisma";

export const { handlers, auth, signIn, signOut } = NextAuth({
  adapter: PrismaAdapter(prisma),
  providers: [
    Google({
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
      authorization: {
        params: {
          prompt: "select_account",
        },
      },
    }),
  ],
  session: { strategy: "database" },
  pages: {
    signIn: "/auth/signin",
  },
  callbacks: {
    session({ session, user }) {
      session.user.id = user.id;
      return session;
    },
  },
});
```

## Step 7: Create `src/lib/utils.ts`

```typescript
import { auth } from "./auth";

export async function getAuthenticatedUser(): Promise<{
  id: string;
  name?: string | null;
  email?: string | null;
  image?: string | null;
} | null> {
  const session = await auth();
  if (!session?.user?.id) return null;
  return { ...session.user, id: session.user.id };
}

// Re-export client utils for server-side use
export { formatDate } from "./client-utils";
```

## Step 8: Create `src/lib/client-utils.ts`

```typescript
export function formatDate(date: Date): string {
  const now = new Date();
  const d = new Date(date);
  const diffMs = now.getTime() - d.getTime();
  const diffDays = Math.floor(diffMs / (1000 * 60 * 60 * 24));

  if (diffDays === 0) return "Today";
  if (diffDays === 1) return "Yesterday";

  return d.toLocaleDateString("en-US", {
    weekday: "short",
    month: "short",
    day: "numeric",
    year: d.getFullYear() !== now.getFullYear() ? "numeric" : undefined,
  });
}
```

## Step 9: Create `src/app/api/auth/[...nextauth]/route.ts`

```typescript
import { handlers } from "@/lib/auth";
export const { GET, POST } = handlers;
```

## Step 10: Create `src/components/auth/SessionProvider.tsx`

```typescript
"use client";

import { SessionProvider as NextAuthSessionProvider } from "next-auth/react";

export default function SessionProvider({
  children,
}: {
  children: React.ReactNode;
}) {
  return <NextAuthSessionProvider>{children}</NextAuthSessionProvider>;
}
```

## Step 11: Create `src/app/auth/signin/page.tsx`

```typescript
"use client";

import { signIn } from "next-auth/react";

export default function SignInPage() {
  return (
    <div className="min-h-screen flex items-center justify-center bg-[#0c0d12]">
      <div className="w-full max-w-xs p-6 rounded-2xl bg-[rgba(255,255,255,0.04)]">
        <div className="text-center mb-6">
          <h1 className="text-xl font-semibold text-white">APP_TITLE</h1>
        </div>
        <button
          onClick={() => signIn("google", { callbackUrl: "/" })}
          className="w-full flex items-center justify-center gap-3 px-5 py-2.5 rounded-xl bg-white text-gray-800 text-sm font-medium hover:bg-gray-100 transition-colors cursor-pointer"
        >
          <svg className="w-4 h-4" viewBox="0 0 24 24">
            <path fill="#4285F4" d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92a5.06 5.06 0 01-2.2 3.32v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.1z" />
            <path fill="#34A853" d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z" />
            <path fill="#FBBC05" d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l2.85-2.22.81-.62z" />
            <path fill="#EA4335" d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z" />
          </svg>
          Sign in with Google
        </button>
      </div>
    </div>
  );
}
```

Replace `APP_TITLE` with the user's app title.

## Step 12: Update `src/app/layout.tsx`

```typescript
import type { Metadata } from "next";
import "./globals.css";
import SessionProvider from "@/components/auth/SessionProvider";

export const metadata: Metadata = {
  title: "APP_TITLE",
  description: "APP_DESCRIPTION",
  other: {
    "google-signin-client_id": "", // Suppress Google One Tap / FedCM
  },
};

export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <html lang="en" className="h-full antialiased">
      <body className="min-h-full">
        <SessionProvider>{children}</SessionProvider>
      </body>
    </html>
  );
}
```

Replace `APP_TITLE` and `APP_DESCRIPTION`.

## Step 13: Setup `src/app/globals.css`

```css
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
@import "tailwindcss";

@theme inline {
  --color-app-bg: #0c0d12;
  --color-app-surface: rgba(255, 255, 255, 0.05);
  --color-app-surface-hover: rgba(255, 255, 255, 0.08);
  --color-app-border: rgba(255, 255, 255, 0.08);
  --color-app-border-hover: rgba(255, 255, 255, 0.15);
  --color-app-accent: #6C9EF8;
  --color-app-accent-hover: #85B1FA;
  --color-app-accent-dim: rgba(108, 158, 248, 0.12);
  --color-app-text: #E8E8E8;
  --color-app-text-secondary: #999999;
  --color-app-text-muted: #666666;
  --color-app-sidebar: #141519;
  --color-app-card: rgba(255, 255, 255, 0.04);
  --color-app-card-hover: rgba(255, 255, 255, 0.07);
  --color-app-danger: #E05555;
  --font-sans: 'Inter', system-ui, sans-serif;
}

* {
  scrollbar-width: thin;
  scrollbar-color: rgba(255, 255, 255, 0.15) transparent;
}

body {
  background: var(--color-app-bg);
  color: var(--color-app-text);
  font-family: var(--font-sans);
}

::selection {
  background: rgba(108, 158, 248, 0.35);
  color: white;
}
```

## Step 14: Create `.env.local`

```
DATABASE_URL=<cockroachdb-connection-string>
GOOGLE_CLIENT_ID=<google-oauth-client-id>
GOOGLE_CLIENT_SECRET=<google-oauth-client-secret>
NEXTAUTH_SECRET=<generated-secret>
NEXTAUTH_URL=http://localhost:3000
AUTH_SECRET=<same-as-nextauth-secret>
AUTH_TRUST_HOST=true
```

## Step 15: Update `.gitignore`

Add:
```
src/generated/prisma
```

## Step 16: Run Prisma Migration

```bash
npx prisma migrate dev --name init
npx prisma generate
```

## Step 17: Copy `.claude/` Directory

Copy the `.claude/` directory and `CLAUDE.md` from the template repo into the new project root so all rules, skills, and hooks are available.

## Step 18: Verify

```bash
npm run dev
```

Navigate to `http://localhost:3000` — should redirect to sign-in page. Sign in with Google — should create user in DB and redirect to home.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/noamLeiv13) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

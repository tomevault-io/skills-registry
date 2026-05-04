---
name: api-routes
description: Next.js API Routes - Route handlers, middleware, edge runtime Use when this capability is needed.
metadata:
  author: neversight
---

# Api Routes Skill

## Overview

Build API endpoints with Next.js Route Handlers and middleware.

## Capabilities

- **Route Handlers**: app/api/route.ts files
- **HTTP Methods**: GET, POST, PUT, DELETE, PATCH
- **Request/Response**: Web API standard
- **Middleware**: Edge runtime processing
- **Dynamic Routes**: [param] patterns

## Examples

```ts
// app/api/users/route.ts
import { NextResponse } from 'next/server'

export async function GET() {
  const users = await db.users.findMany()
  return NextResponse.json(users)
}

export async function POST(request: Request) {
  const body = await request.json()
  const user = await db.users.create(body)
  return NextResponse.json(user, { status: 201 })
}

// app/api/users/[id]/route.ts
export async function GET(
  request: Request,
  { params }: { params: { id: string } }
) {
  const user = await db.users.findById(params.id)
  return NextResponse.json(user)
}
```

## Middleware Example

```ts
// middleware.ts
export function middleware(request: NextRequest) {
  const token = request.cookies.get('token')
  if (!token) {
    return NextResponse.redirect(new URL('/login', request.url))
  }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

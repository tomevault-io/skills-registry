---
name: api-standards
description: This skill should be used when the user asks to \"create an API route\", \"add an endpoint\", \"build a REST API\", \"write a server action\", \"handle API errors\", \"add authentication to API\", or is working with Next.js API routes, server actions, and backend logic. Use when this capability is needed.
metadata:
  author: enesdmc0
---

# API Development Standards

## Overview

Standards for building API routes and server actions in Next.js 15 App Router with TypeScript, Zod validation, Drizzle ORM, and Supabase authentication.

## API Route Standards

### File Structure
- Route handler: `src/app/api/[resource]/route.ts`
- Dynamic route: `src/app/api/[resource]/[id]/route.ts`
- Grouped routes: `src/app/api/(v1)/[resource]/route.ts`

### Standard GET Handler

```typescript
import { NextRequest, NextResponse } from "next/server"
import { db } from "@/db"
import { users } from "@/db/schema/users"
import { eq } from "drizzle-orm"

export async function GET(request: NextRequest) {
  try {
    const { searchParams } = new URL(request.url)
    const page = Number(searchParams.get("page") ?? "1")
    const limit = Number(searchParams.get("limit") ?? "10")
    const offset = (page - 1) * limit

    const results = await db.select().from(users).limit(limit).offset(offset)

    return NextResponse.json({ data: results, page, limit })
  } catch {
    return NextResponse.json({ error: "Internal server error" }, { status: 500 })
  }
}
```

### Standard POST Handler

```typescript
import { NextRequest, NextResponse } from "next/server"
import { z } from "zod"
import { db } from "@/db"
import { users } from "@/db/schema/users"

const createUserSchema = z.object({
  name: z.string().min(2),
  email: z.string().email(),
})

export async function POST(request: NextRequest) {
  try {
    const body = await request.json()
    const validated = createUserSchema.parse(body)

    const [user] = await db.insert(users).values(validated).returning()

    return NextResponse.json(user, { status: 201 })
  } catch (error) {
    if (error instanceof z.ZodError) {
      return NextResponse.json(
        { error: "Validation failed", details: error.errors },
        { status: 400 }
      )
    }
    return NextResponse.json({ error: "Internal server error" }, { status: 500 })
  }
}
```

### Dynamic Route Parameter (Next.js 15)

In Next.js 15, params is a Promise - always await it:

```typescript
export async function GET(
  request: NextRequest,
  { params }: { params: Promise<{ id: string }> }
) {
  const { id } = await params

  const [user] = await db.select().from(users).where(eq(users.id, id))

  if (!user) {
    return NextResponse.json({ error: "Not found" }, { status: 404 })
  }
  return NextResponse.json(user)
}
```

## Server Actions Standards

### Location and Structure
- File: `src/actions/[resource].ts`
- Always start with `"use server"` directive
- Zod validation is mandatory
- Return typed result object

### Standard Server Action

```typescript
"use server"

import { z } from "zod"
import { revalidatePath } from "next/cache"
import { db } from "@/db"
import { posts } from "@/db/schema/posts"

const createPostSchema = z.object({
  title: z.string().min(1),
  content: z.string().min(10),
})

type ActionResult = {
  success: boolean
  error?: string
}

export async function createPost(formData: FormData): Promise<ActionResult> {
  try {
    const validated = createPostSchema.parse({
      title: formData.get("title"),
      content: formData.get("content"),
    })

    await db.insert(posts).values(validated)
    revalidatePath("/posts")

    return { success: true }
  } catch (error) {
    if (error instanceof z.ZodError) {
      return { success: false, error: error.errors[0].message }
    }
    return { success: false, error: "Something went wrong" }
  }
}
```

## Authentication Pattern

```typescript
import { createClient } from "@/lib/supabase/server"

// In API route or server action
const supabase = await createClient()
const { data: { user }, error } = await supabase.auth.getUser()

if (!user) {
  return NextResponse.json({ error: "Unauthorized" }, { status: 401 })
}
```

## Error Handling Strategy

| Status | When to Use | Example |
|--------|------------|---------|
| 200 | Successful GET/PUT/PATCH | Data returned/updated |
| 201 | Successful POST (created) | New resource created |
| 400 | Invalid input | Zod validation failed |
| 401 | Not authenticated | No valid session |
| 403 | Not authorized | User lacks permission |
| 404 | Resource not found | ID doesn't exist |
| 409 | Conflict | Duplicate email |
| 500 | Server error | Unexpected exception |

## Anti-Patterns

- Never use raw SQL - always use Drizzle ORM
- Never trust client input - always validate with Zod
- Never expose internal errors to client
- Never skip authentication on protected routes
- Never use GET for mutations
- Never return sensitive data (passwords, tokens) in responses

## Additional Resources

- **references/error-handling.md** - Advanced error handling strategies
- **references/auth-patterns.md** - Authentication and authorization patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/enesdmc0) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

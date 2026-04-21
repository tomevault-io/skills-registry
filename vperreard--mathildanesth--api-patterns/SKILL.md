---
name: api-patterns
description: > Use when this capability is needed.
metadata:
  author: vperreard
---

# API Patterns - Quick Reference

**Token Efficiency**: ~600 tokens vs 5k+ pour recherche manuelle
**Last Update**: 25 November 2025

## Quick Stats
- **470+ endpoints** dans /api/
- **Pattern standard** avec error handling uniforme
- **Validation Zod** obligatoire sur POST/PUT/PATCH
- **Mapping PascalCase→camelCase** pour réponses

---

## Pattern Standard - GET

```typescript
import { NextRequest, NextResponse } from 'next/server';
import { getServerSession } from 'next-auth';
import { authOptions } from '@/lib/auth';
import { prisma } from '@/lib/prisma';

export async function GET(req: NextRequest) {
  try {
    // 1. Auth check
    const session = await getServerSession(authOptions);
    if (!session?.user) {
      return NextResponse.json({ error: 'Non autorisé' }, { status: 401 });
    }

    // 2. Parse query params
    const { searchParams } = new URL(req.url);
    const siteId = searchParams.get('siteId');

    // 3. Prisma query avec relations
    const items = await prisma.model.findMany({
      where: { siteId: siteId || undefined },
      include: {
        RelatedModel: true,  // PascalCase from Prisma
      },
      orderBy: { createdAt: 'desc' }
    });

    // 4. Map PascalCase → camelCase pour frontend
    const mapped = items.map(item => ({
      ...item,
      relatedModel: item.RelatedModel,  // camelCase for frontend
      RelatedModel: undefined,
    }));

    return NextResponse.json(mapped);
  } catch (error) {
    console.error('GET /api/xxx error:', error);
    return NextResponse.json(
      { error: 'Erreur serveur' },
      { status: 500 }
    );
  }
}
```

---

## Pattern Standard - POST avec Validation

```typescript
import { NextRequest, NextResponse } from 'next/server';
import { getServerSession } from 'next-auth';
import { authOptions } from '@/lib/auth';
import { prisma } from '@/lib/prisma';
import { z } from 'zod';

// Schema Zod OBLIGATOIRE
const createSchema = z.object({
  name: z.string().min(1, 'Nom requis').max(100),
  siteId: z.string().min(1, 'Site requis'),
  type: z.enum(['TYPE_A', 'TYPE_B']),
  description: z.string().optional(),
});

export async function POST(req: NextRequest) {
  try {
    // 1. Auth check
    const session = await getServerSession(authOptions);
    if (!session?.user) {
      return NextResponse.json({ error: 'Non autorisé' }, { status: 401 });
    }

    // 2. Parse et validate body
    const body = await req.json();
    const validation = createSchema.safeParse(body);

    if (!validation.success) {
      return NextResponse.json(
        { error: 'Données invalides', details: validation.error.flatten() },
        { status: 400 }
      );
    }

    const data = validation.data;

    // 3. Business logic checks
    const exists = await prisma.model.findFirst({
      where: { name: data.name, siteId: data.siteId }
    });

    if (exists) {
      return NextResponse.json(
        { error: 'Un élément avec ce nom existe déjà' },
        { status: 409 }
      );
    }

    // 4. Create avec updatedAt OBLIGATOIRE (pas de @default)
    const created = await prisma.model.create({
      data: {
        ...data,
        createdById: session.user.id,
        updatedAt: new Date(),  // REQUIS - pas de @default
      }
    });

    return NextResponse.json(created, { status: 201 });
  } catch (error) {
    console.error('POST /api/xxx error:', error);
    return NextResponse.json(
      { error: 'Erreur serveur' },
      { status: 500 }
    );
  }
}
```

---

## Pattern Standard - Dynamic Route [id]

```typescript
// app/api/items/[id]/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { getServerSession } from 'next-auth';
import { authOptions } from '@/lib/auth';
import { prisma } from '@/lib/prisma';

type RouteParams = { params: Promise<{ id: string }> };

export async function GET(req: NextRequest, { params }: RouteParams) {
  try {
    const session = await getServerSession(authOptions);
    if (!session?.user) {
      return NextResponse.json({ error: 'Non autorisé' }, { status: 401 });
    }

    const { id } = await params;  // Await params in Next.js 15

    // Parse ID selon le type (Int vs String)
    const numericId = parseInt(id, 10);
    if (isNaN(numericId)) {
      return NextResponse.json({ error: 'ID invalide' }, { status: 400 });
    }

    const item = await prisma.model.findUnique({
      where: { id: numericId },
      include: { RelatedModel: true }
    });

    if (!item) {
      return NextResponse.json({ error: 'Non trouvé' }, { status: 404 });
    }

    // Map pour frontend
    return NextResponse.json({
      ...item,
      relatedModel: item.RelatedModel,
      RelatedModel: undefined,
    });
  } catch (error) {
    console.error('GET /api/items/[id] error:', error);
    return NextResponse.json({ error: 'Erreur serveur' }, { status: 500 });
  }
}

export async function DELETE(req: NextRequest, { params }: RouteParams) {
  try {
    const session = await getServerSession(authOptions);
    if (!session?.user) {
      return NextResponse.json({ error: 'Non autorisé' }, { status: 401 });
    }

    const { id } = await params;
    const numericId = parseInt(id, 10);

    await prisma.model.delete({
      where: { id: numericId }
    });

    return NextResponse.json({ success: true });
  } catch (error) {
    console.error('DELETE /api/items/[id] error:', error);
    return NextResponse.json({ error: 'Erreur serveur' }, { status: 500 });
  }
}
```

---

## Checklist Création API

1. [ ] Auth check avec `getServerSession(authOptions)`
2. [ ] Validation Zod pour POST/PUT/PATCH
3. [ ] `updatedAt: new Date()` sur create/update
4. [ ] `siteId` inclus si modèle le requiert
5. [ ] Mapping PascalCase→camelCase pour réponses
6. [ ] Error handling avec status codes appropriés
7. [ ] Logs `console.error` pour debugging

---

## Status Codes Standards

| Code | Usage |
|------|-------|
| 200 | GET success, PUT/PATCH success |
| 201 | POST success (création) |
| 400 | Validation error, bad request |
| 401 | Non authentifié |
| 403 | Non autorisé (permission) |
| 404 | Ressource non trouvée |
| 409 | Conflit (doublon) |
| 500 | Erreur serveur |

---

## Ressources Progressives

- **`resources/error-handling.md`** - Patterns d'erreur détaillés
- **`resources/pagination.md`** - Pagination avec cursors
- **`resources/batch-operations.md`** - Opérations en lot

**Reference**: PRISMA_RELATIONS_MAPPING.md, ARCHITECTURE_INDEX.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vperreard) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

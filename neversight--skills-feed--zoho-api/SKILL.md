---
name: zoho-api
description: | Use when this capability is needed.
metadata:
  author: neversight
---

# Zoho API Integration

## Quick Reference

| API | Base URL |
|-----|----------|
| Zoho Inventory | `https://www.zohoapis.com/inventory/v1` |
| Zoho Books | `https://www.zohoapis.com/books/v3` |

**Organization ID**: `748369814`

## Code Files

| File | Purpose |
|------|---------|
| `src/lib/zoho/client.ts` | OAuth client, token caching, `zohoFetch` |
| `src/lib/zoho/products.ts` | Products, stock extraction |
| `src/lib/zoho/price-lists.ts` | Price list constants and fetching |
| `src/lib/zoho/customers.ts` | Customer lookup by email |
| `src/lib/zoho/orders.ts` | Sales orders |
| `src/lib/zoho/invoices.ts` | Invoices |
| `src/lib/zoho/payments.ts` | Payments |
| `src/lib/zoho/credit-notes.ts` | Credit notes |

## Using zohoFetch

```typescript
import { zohoFetch } from '@/lib/zoho/client';

// GET request
const data = await zohoFetch('/inventory/v1/items', {
  params: {
    organization_id: process.env.ZOHO_ORGANIZATION_ID,
    page: 1,
    per_page: 100,
  },
});

// Single item with locations
const item = await zohoFetch(`/inventory/v1/items/${itemId}`, {
  params: { organization_id: process.env.ZOHO_ORGANIZATION_ID },
});
```

## Token Caching (CRITICAL)

```
Memory Cache → Upstash Redis → Zoho OAuth Refresh
              (50-min TTL)    (rate limit: 10s guard)
```

If all prices show "Contact for price", check:
1. Upstash env vars in Vercel
2. `UPSTASH_REDIS_REST_URL` and `UPSTASH_REDIS_REST_TOKEN`
3. Run: `curl https://www.tsh.sale/api/debug/token`

## Common Endpoints

### Inventory API
```
GET /items                      # List products
GET /items/{id}                 # Single product (includes locations)
GET /categories                 # List categories
GET /pricebooks/{id}            # Pricebook with items
```

### Books API
```
GET /contacts                   # List customers
GET /salesorders                # Sales orders
GET /invoices                   # Invoices
GET /customerpayments           # Payments
GET /creditnotes                # Credit notes
```

## Caching with unstable_cache

```typescript
import { unstable_cache } from 'next/cache';

const getCachedProducts = unstable_cache(
  async () => await fetchProducts(),
  ['products'],
  { revalidate: 3600, tags: ['products'] }
);
```

Revalidate cache:
```bash
curl "https://www.tsh.sale/api/revalidate?tag=products&secret=tsh-revalidate-2024"
```

## Error Handling Pattern

```typescript
try {
  const data = await zohoFetch('/inventory/v1/items', { ... });
} catch (error) {
  if (error.message.includes('401')) {
    // Token expired - auto-refreshes
  } else if (error.message.includes('429')) {
    // Rate limited - wait and retry
  }
}
```

## Creating New API Route

```typescript
// src/app/api/zoho/[resource]/route.ts
import { NextResponse } from 'next/server';
import { zohoFetch } from '@/lib/zoho/client';

export async function GET(request: Request) {
  const data = await zohoFetch('/inventory/v1/items', {
    params: {
      organization_id: process.env.ZOHO_ORGANIZATION_ID,
    },
  });

  return NextResponse.json(data);
}
```

## Rate Limits

| API | Limit |
|-----|-------|
| OAuth Refresh | ~100/minute |
| Inventory API | 100/minute |
| Books API | 100/minute |

## Debug Commands

```bash
# Check token
curl "https://www.tsh.sale/api/debug/token"

# Check prices
curl "https://www.tsh.sale/api/debug/prices"

# Check stock
curl "https://www.tsh.sale/api/debug/stock"

# Revalidate cache
curl "https://www.tsh.sale/api/revalidate?tag=all&secret=tsh-revalidate-2024"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

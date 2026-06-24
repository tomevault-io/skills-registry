---
name: api-development
description: Develop and test API routes in the Next.js App Router. Use this when the user asks to create, modify, test, or debug API endpoints, handle API responses, validate request parameters, or work with the /api directory. Use when this capability is needed.
metadata:
  author: ruchernchong
---

# API Development Skill

This Skill helps develop, test, and debug API routes in the IsLeapYear Next.js application.

## When to Use

- Creating new API endpoints
- Modifying existing API routes
- Testing API responses
- Debugging API errors
- Validating request parameters
- Working with API response helpers

## Key Patterns

### API Route Structure

All API routes follow Next.js 15 App Router patterns with async params:

```typescript
export const GET = async (
  request: NextRequest,
  { params }: { params: Promise<{ year: string }> }
) => {
  const { year: yearParam } = await params;
  const year = Number.parseInt(yearParam, 10);

  if (Number.isNaN(year)) {
    return errorResponse("Invalid year parameter");
  }

  return successResponse({ result: someData });
};
```

### Response Helpers

Located in `src/utils/response.ts`:

- `successResponse<T>(data: T)` - Returns 200 with standardized format
- `errorResponse(message: string, status?: number)` - Returns error response

Response format:
```json
{
  "status": 200,
  "data": { ... },
  "meta": { "timestamp": "2025-01-15T..." }
}
```

## API Directory Structure

```
src/app/api/
├── check/              # Leap year check endpoints
│   ├── [year]/        # Single year: /api/check/2024
│   ├── batch/         # Batch checks
│   └── route.ts       # Current year
├── calendar/          # Multi-calendar support
│   └── [type]/check/[year]/
└── stats/             # Statistics
    ├── range/
    └── distribution/
```

## Testing Commands

```bash
# Test current year endpoint
curl http://localhost:3000/api/check

# Test specific year
curl http://localhost:3000/api/check/2024

# Test batch endpoint
curl -X POST http://localhost:3000/api/check/batch \
  -H "Content-Type: application/json" \
  -d '{"years": [2024, 2025, 2026]}'

# Test calendar type
curl http://localhost:3000/api/calendar/gregorian/check/2024
```

## Common Tasks

### 1. Creating a New Endpoint

1. Determine the route path (e.g., `/api/check/validate/[year]`)
2. Create the directory structure
3. Create `route.ts` with GET/POST handlers
4. Use response helpers from `utils/response.ts`
5. Validate all inputs
6. Test with curl commands

### 2. Validating Parameters

Always validate:
- Year ranges (1582-9999 for Gregorian)
- Number parsing (use `Number.parseInt` and check `Number.isNaN`)
- Required fields for POST requests
- Calendar types (gregorian, julian, hebrew, chinese)

### 3. Error Handling

Return appropriate errors:
- 400 for invalid parameters
- 404 for not found
- 500 for server errors

Use `errorResponse()` helper for consistency.

## Important Notes

- Always use `async` functions for route handlers
- Always `await params` in dynamic routes (Next.js 15 requirement)
- Use TypeScript for all routes
- Import response helpers: `import { successResponse, errorResponse } from "@/utils/response"`
- Test locally before deployment
- Follow the satirical tone for error messages when appropriate

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ruchernchong) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

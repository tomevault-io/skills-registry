---
name: zod-validation
description: Implement robust Zod validation schemas for type-safe data validation in Next.js systems with security-first approach, tenant isolation, and comprehensive error handling. Use when building secure API endpoints, forms, and data processing with Fortune-500 validation standards. Use when this capability is needed.
metadata:
  author: artisanclarinets
---

# Zod Validation

A comprehensive skill for implementing robust Zod validation schemas in Next.js 16 + React 19 App Router systems, following Fortune-500 security standards with tenant isolation, comprehensive error handling, and type-safe data validation.

## Quick Start

Apply Zod validation for secure, type-safe data handling:

1. **API Route Validation** (`app/api/admin/leads/route.ts`):
   ```typescript
   const createLeadSchema = z.object({
     name: z.string().min(1, "Name is required"),
     email: z.string().email("Invalid email"),
     message: z.string().optional(),
     source: z.string().optional(),
     status: z.string().optional(),
     budget: z.string().optional(),
     website: z.string().optional(),
   });

   export async function POST(req: NextRequest) {
     return adminMutation(req, { permissions: ["leads.write"], audit: { action: "create_lead", resource: "lead" } }, async (user, body) => {
       const validatedData = createLeadSchema.parse(body);
       // ... rest of implementation
     });
   }
   ```

2. **Form Validation** (`components/admin/leads/lead-form.tsx`):
   ```typescript
   const leadSchema = z.object({
     name: z.string().min(1, "Name is required"),
     email: z.string().email("Invalid email"),
     status: z.string().min(1, "Status is required"),
     source: z.string().optional(),
     budget: z.string().optional(),
     website: z.string().optional(),
     message: z.string().optional(),
   });

   const form = useForm<LeadFormValues>({
     resolver: zodResolver(leadSchema),
     defaultValues: { /* ... */ },
   });
   ```

3. **Error Handling** (`lib/api/errors.ts`):
   ```typescript
   if (error instanceof ZodError) {
     return createErrorResponse(
       "UNPROCESSABLE_ENTITY",
       "Validation failed",
       error.errors,
       requestId
     );
   }
   ```

## Core Concepts

### Schema Definition Patterns

- **Object Schemas**: Define structured data validation with `z.object()`
- **Primitive Types**: String, number, boolean with validation rules
- **Optional Fields**: Use `.optional()` for non-required fields
- **Type Coercion**: Use `z.coerce` for automatic type conversion
- **Custom Messages**: Provide user-friendly error messages

### Security Integration

- **Tenant Scoping**: Always validate tenant context in multi-tenant systems
- **Input Sanitization**: Zod automatically handles type safety and basic sanitization
- **CSRF Protection**: Integrate with existing CSRF validation patterns
- **Audit Logging**: Log validation failures for security monitoring

### Error Handling Strategy

- **Standardized Responses**: Use consistent error response format
- **Detailed Validation Errors**: Return specific field-level error messages
- **Request Tracking**: Include request IDs for debugging
- **Security Logging**: Log validation failures without exposing sensitive data

## Workflows

### 1. Creating API Route Validation

**Purpose**: Implement secure server-side validation for admin API endpoints.

**Steps**:
1. Define Zod schema matching Prisma model structure
2. Use `adminMutation` or `adminRead` wrapper for security
3. Parse request body with schema validation
4. Handle ZodError with standardized error responses
5. Include audit metadata for privileged operations

**Example**: Lead Creation API
```typescript
const createLeadSchema = z.object({
  name: z.string().min(1, "Name is required"),
  email: z.string().email("Invalid email"),
  message: z.string().optional(),
  source: z.string().optional(),
  status: z.string().optional(),
  budget: z.string().optional(),
  website: z.string().optional(),
});

export async function POST(req: NextRequest) {
  return adminMutation(req, { 
    permissions: ["leads.write"], 
    audit: { action: "create_lead", resource: "lead" } 
  }, async (user, body) => {
    const validatedData = createLeadSchema.parse(body);

    const lead = await prisma.lead.create({
      data: {
        ...validatedData,
        ...(user.tenantId && { tenantId: user.tenantId }),
      },
    });

    return { data: lead, status: 201 };
  });
}
```

### 2. Implementing Form Validation

**Purpose**: Provide client-side validation with seamless UX and server-side enforcement.

**Steps**:
1. Define shared Zod schema for both client and server
2. Use `zodResolver` with react-hook-form
3. Display field-level error messages
4. Handle form submission with CSRF protection
5. Show loading states and success feedback

**Example**: Lead Management Form
```typescript
const leadSchema = z.object({
  name: z.string().min(1, "Name is required"),
  email: z.string().email("Invalid email"),
  status: z.string().min(1, "Status is required"),
  source: z.string().optional(),
  budget: z.string().optional(),
  website: z.string().optional(),
  message: z.string().optional(),
});

type LeadFormValues = z.infer<typeof leadSchema>;

export function LeadForm({ initialData }: LeadFormProps) {
  const form = useForm<LeadFormValues>({
    resolver: zodResolver(leadSchema),
    defaultValues: {
      name: initialData?.name || "",
      email: initialData?.email || "",
      status: initialData?.status || "new",
      // ... other fields
    },
  });

  const onSubmit = async (data: LeadFormValues) => {
    const res = await fetchWithCsrf("/api/admin/leads", {
      method: "POST",
      body: JSON.stringify(data),
    });
    // ... handle response
  };
}
```

### 3. Building Filter and Pagination Schemas

**Purpose**: Validate query parameters for list endpoints with proper typing.

**Steps**:
1. Define pagination schema with cursor and limits
2. Create filter schemas for common query parameters
3. Parse URL search params with validation
4. Build Prisma queries with validated parameters
5. Return paginated results with proper cursors

**Example**: Pagination and Filtering
```typescript
export const paginationSchema = z.object({
  cursor: z.string().optional(),
  take: z.coerce.number().int().min(1).max(100).default(20),
  direction: z.enum(["forward", "backward"]).default("forward"),
});

export const commonFiltersSchema = z.object({
  q: z.string().optional(), // Search query
  status: z.string().optional(),
  tenantId: z.string().optional(),
  dateFrom: z.string().datetime().optional(),
  dateTo: z.string().datetime().optional(),
  includeDeleted: z.coerce.boolean().default(false),
});

export function parsePaginationParams(searchParams: URLSearchParams) {
  return paginationSchema.parse({
    cursor: searchParams.get("cursor") || undefined,
    take: searchParams.get("take") || undefined,
    direction: searchParams.get("direction") || undefined,
  });
}
```

### 4. Error Response Standardization

**Purpose**: Provide consistent, secure error handling across all API endpoints.

**Steps**:
1. Define error code enum for different scenarios
2. Create standardized error response format
3. Handle Zod validation errors specifically
4. Include request IDs for tracking
5. Log errors securely without exposing sensitive data

**Example**: Error Handling Utility
```typescript
export type ErrorCode = 
  | "UNAUTHORIZED"
  | "FORBIDDEN" 
  | "NOT_FOUND"
  | "BAD_REQUEST"
  | "CONFLICT"
  | "UNPROCESSABLE_ENTITY"
  | "RATE_LIMIT_EXCEEDED"
  | "INTERNAL_SERVER_ERROR"
  | "SERVICE_UNAVAILABLE";

export function normalizeError(error: unknown, requestId?: string): NextResponse<ApiError> {
  if (error instanceof ZodError) {
    return createErrorResponse(
      "UNPROCESSABLE_ENTITY",
      "Validation failed",
      error.errors,
      requestId
    );
  }

  if (error && typeof error === "object" && "code" in error) {
    const prismaError = error as any;
    if (prismaError.code === "P2002") {
      return createErrorResponse("CONFLICT", "Duplicate entry", prismaError.meta, requestId);
    }
  }

  return createErrorResponse("INTERNAL_SERVER_ERROR", "An unexpected error occurred", undefined, requestId);
}
```

## Advanced Features

### Schema Composition and Reuse

Advanced schema patterns for complex validation:

```typescript
// Base user schema
const baseUserSchema = z.object({
  email: z.string().email(),
  name: z.string().min(1),
});

// Extended schema for registration
const registerUserSchema = baseUserSchema.extend({
  password: z.string().min(8),
  confirmPassword: z.string(),
}).refine((data) => data.password === data.confirmPassword, {
  message: "Passwords don't match",
  path: ["confirmPassword"],
});

// Schema for updates (all fields optional)
const updateUserSchema = baseUserSchema.partial();
```

### Custom Validation Rules

Business logic validation with Zod transforms:

```typescript
const projectSchema = z.object({
  name: z.string().min(1),
  budget: z.number().positive(),
  startDate: z.date(),
  endDate: z.date(),
}).refine((data) => data.endDate > data.startDate, {
  message: "End date must be after start date",
  path: ["endDate"],
});

// Custom email domain validation
const corporateEmailSchema = z.string().email().refine(
  (email) => email.endsWith("@company.com"),
  "Must use company email address"
);
```

### Union Types and Discriminated Unions

Handle different data shapes with discriminated unions:

```typescript
const paymentMethodSchema = z.discriminatedUnion("type", [
  z.object({
    type: z.literal("credit_card"),
    cardNumber: z.string().regex(/^\d{16}$/),
    expiryDate: z.string().regex(/^\d{2}\/\d{2}$/),
    cvv: z.string().regex(/^\d{3}$/),
  }),
  z.object({
    type: z.literal("bank_transfer"),
    accountNumber: z.string(),
    routingNumber: z.string(),
  }),
]);
```

## Troubleshooting

### Common Issues

**Schema validation fails unexpectedly**: Check that all required fields are provided and types match. Use `.optional()` for optional fields.

**Type inference not working**: Ensure you're using `z.infer<typeof schema>` correctly and that TypeScript can infer the types.

**Error messages not showing**: Verify that form components are properly connected to react-hook-form and that `FormMessage` components are included.

**Performance issues with large schemas**: Break down large schemas into smaller, reusable pieces and use `.partial()` for updates.

**Tenant scoping not working**: Ensure tenant validation is applied at the database query level, not just in Zod schemas.

## Best Practices

- Always define schemas at the boundary (API routes, form components)
- Use shared schemas between client and server for consistency
- Provide meaningful error messages for better UX
- Include tenant scoping in multi-tenant applications
- Handle ZodError specifically in error handling middleware
- Use type inference for automatic TypeScript types
- Validate query parameters for list endpoints
- Include audit logging for sensitive operations

## Examples

See the codebase for complete implementations:
- [`lib/api/pagination.ts`](lib/api/pagination.ts:1) - Pagination schema and utilities
- [`lib/api/errors.ts`](lib/api/errors.ts:1) - Error handling with Zod integration
- [`lib/api/filters/common.ts`](lib/api/filters/common.ts:1) - Common filter schemas
- [`app/api/admin/leads/route.ts`](app/api/admin/leads/route.ts:1) - API route with validation
- [`components/admin/leads/lead-form.tsx`](components/admin/leads/lead-form.tsx:1) - Form validation implementation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/artisanclarinets) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

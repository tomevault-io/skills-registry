---
name: client-portal-full-stack-feature
description: Rules for building full-stack features with frontend and backend changes. Use when this capability is needed.
metadata:
  author: codyktam
---

## Overview
Follow these rules when creating features that span the client-portal frontend and Node.js backend.

## Frontend: API Calls

### Define the API function
Add an exported function in `/client-portal/app/jupiter-api/index.ts` using the request wrappers:

```ts
// Example: GET request
export const getInvoices = (clientId: string) => 
  get<Invoice[]>(`/customer/client/invoices?clientId=${clientId}`);

// Example: POST request  
export const createPayment = (data: CreatePaymentRequest) =>
  post<Payment>('/customer/client/payments', data);
```

### Call from React components
```js
// For GET requests
const { data, isLoading } = useQuery({
  queryKey: ['invoices', clientId],
  queryFn: () => getInvoices(clientId),
});

// For mutations (POST/PATCH/DELETE)
const mutation = useMutation({
  mutationFn: createPayment,
  onSuccess: () => queryClient.invalidateQueries(['invoices']),
});
```

## Frontend: Components
Refer to @./client-portal-frontend-components

## Backend: Routes

### Route Structure
Main router: /backend/routes/customer/index.ts (mounted at /customer)
Auth routes: /backend/routes/customer/auth.ts (mounted at /customer/auth)
Client routes: /backend/routes/customer/client.ts (mounted at /customer/client)

### 1. Define the route in the appropriate subrouter file (route definition only):
// /backend/routes/customer/client.ts
router.get('/invoices', authMiddleware, invoiceController.getInvoices);

### 2. Implement the handler in a semantically appropriate file under /backend/:
Controllers: /backend/controllers/ - request/response handling
Services: /backend/services/ - business logic
Middleware: /backend/middleware/ - auth, validation, etc.

### DO NOT
- Write handler implementation directly in route files
- Mix business logic into route definitions
- Skip auth middleware on protected routes

## Testing

E2E tests use Playwright and live in `/client-portal/e2e/`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/codyktam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

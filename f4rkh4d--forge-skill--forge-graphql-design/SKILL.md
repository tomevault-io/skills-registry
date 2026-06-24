---
name: forge-graphql-design
description: GraphQL schema design. Nullable defaults, connections (cursor pagination via Relay spec), errors as data not throws, DataLoader for N+1, persisted queries for caching, schema versioning via deprecation, no overfetch via fragments. Contains paste-ready schema patterns and a DataLoader recipe. Use when designing a GraphQL API. Use when this capability is needed.
metadata:
  author: f4rkh4d
---

# forge-graphql-design

You are designing a GraphQL schema that clients will query for years. Default agent-written GraphQL schemas have non-null-everywhere fields that propagate errors up the tree, throw exceptions instead of returning typed errors, offset pagination, and N+1 queries on every list field. This skill exists to fix all of that before the schema ships.

The mental model: **GraphQL is a typed contract.** The schema is read by humans and codegens alike. Choices that look ergonomic in dev (everything non-null) cost you in production (one DB error nulls the entire query). Choices that look "RESTful" (offset pagination) make scaling painful.

## Quick reference (the things you must never ship)

1. Non-null everywhere - especially on fields that can fail at runtime.
2. Offset/page-based pagination on list fields.
3. Throwing exceptions from a resolver instead of returning a typed error union.
4. Resolving a list of `User` by calling `getUser(id)` inside a `.map()` (N+1).
5. `String` for IDs (use `ID`).
6. `String` for money (use a `Money` object with `amountCents: Int!` + `currency: Currency!`).
7. `Date` as a `String` without an explicit format scalar.
8. Removing a field directly instead of `@deprecated` first.
9. Unlimited query depth (allows DoS via deeply nested queries).
10. No persisted-query / allowlist mechanism for production.

## Hard rules

### Nullability

**1. Default to nullable.** Mark `!` (non-null) only when you can guarantee the field is present even on a partial DB failure.

```graphql
# BAD: non-null everywhere
type Order {
  id: ID!
  customer: Customer!
  items: [LineItem!]!
  shipped_at: DateTime!     # what if it's not shipped yet?
}

# GOOD: deliberate nullability
type Order {
  id: ID!
  customer: Customer        # nullable - customer table might be down
  items: [LineItem!]!       # list itself non-null + items non-null
  shipped_at: DateTime      # nullable - not shipped yet is a state
}
```

The cost of `!`: if the field resolves to null at runtime, GraphQL propagates the null up to the nearest nullable parent. A non-null leaf can null the whole query.

**2. List fields:** `[X!]!` (non-null list of non-null items) is usually right - you cannot have null elements in a list, and an empty list is `[]` not `null`. `[X!]` (nullable list) is fine if "no list" is meaningfully different from "empty list."

**3. Scalar inputs:** non-null required parameters, nullable optional.

```graphql
type Query {
  orders(
    customerId: ID
    status: OrderStatus
    first: Int = 50
    after: String
  ): OrderConnection!
}
```

### Pagination (Relay Connection spec)

**4. Cursor-based pagination via the Connection spec.**

```graphql
type Query {
  orders(first: Int = 50, after: String, last: Int, before: String): OrderConnection!
}

type OrderConnection {
  edges: [OrderEdge!]!
  pageInfo: PageInfo!
  totalCount: Int    # optional, expensive
}

type OrderEdge {
  node: Order!
  cursor: String!
}

type PageInfo {
  hasNextPage: Boolean!
  hasPreviousPage: Boolean!
  startCursor: String
  endCursor: String
}
```

**5. `totalCount` is optional and expensive.** Do not return it unless the client asked for it and you can compute it cheaply.

**6. Cap `first` / `last` at 100-200.** Reject larger requests with an `argument_too_large` typed error or default to the cap with a warning header.

### Errors as data

**7. Throwing exceptions from a resolver is reserved for unexpected failures.** For expected business failures (validation, permission denied, not found), return a typed error union.

```graphql
type Mutation {
  createOrder(input: CreateOrderInput!): CreateOrderPayload!
}

union CreateOrderPayload =
  | CreateOrderSuccess
  | ValidationError
  | CustomerNotFoundError
  | OutOfStockError

type CreateOrderSuccess {
  order: Order!
}

type ValidationError {
  message: String!
  fields: [FieldError!]!
}
type FieldError {
  path: String!
  code: String!
  message: String!
}
type CustomerNotFoundError {
  customerId: ID!
}
type OutOfStockError {
  sku: String!
  available: Int!
}
```

Client side:

```graphql
mutation {
  createOrder(input: { customerId: "...", items: [...] }) {
    __typename
    ... on CreateOrderSuccess { order { id total_cents } }
    ... on ValidationError    { fields { path code message } }
    ... on CustomerNotFoundError { customerId }
    ... on OutOfStockError    { sku available }
  }
}
```

**8. Schema-level `errors` array is for the bad cases (validation, syntax, auth).** Business outcomes live in the payload union.

### N+1 and DataLoader

**9. DataLoader for every "fetch by ID" path in a list field.**

```ts
// reference: per-request DataLoader for User by id
import DataLoader from "dataloader";

function createUserLoader(db: DB) {
  return new DataLoader<string, User | null>(async (ids) => {
    const users = await db.users.findManyByIds(ids as string[]);
    const byId = new Map(users.map((u) => [u.id, u]));
    return ids.map((id) => byId.get(id) ?? null);
  });
}

// resolver
const resolvers = {
  Order: {
    async customer(order, _args, ctx) {
      return ctx.loaders.user.load(order.customer_id);
    },
  },
};

// context per request
function buildContext(req: Request) {
  return {
    loaders: {
      user: createUserLoader(db),
      product: createProductLoader(db),
    },
  };
}
```

DataLoader batches all `load(id)` calls in the same tick into a single `findManyByIds` query.

**10. Use field-level caching for hot, immutable data.** DataLoader caches within a request; Redis caches across requests.

### IDs and types

**11. Use `ID` (the built-in scalar) for IDs, not `String`.** GraphQL treats `ID` as opaque - clients should not parse it.

**12. Custom scalars for typed values.**

```graphql
scalar DateTime         # ISO 8601
scalar UUID
scalar EmailAddress
scalar URL

type User {
  id: ID!
  email: EmailAddress!
  created_at: DateTime!
}
```

**13. `Money` as an object, not a `Float`.**

```graphql
type Money {
  amountCents: Int!
  currency: Currency!
}

enum Currency { USD EUR KZT }
```

### Schema versioning

**14. Never remove a field. Deprecate first.**

```graphql
type Order {
  total: Money @deprecated(reason: "Use `totalMoney` instead. Will be removed 2026-12-01.")
  totalMoney: Money!
}
```

**15. Removal happens only after analytics show no client uses it.** Most graph hosting (Apollo Studio, Hasura) provides this.

**16. Additive changes are not breaking:** new field on a type, new optional argument, new enum value (clients should treat unknown enum values as a fallback).

### Query depth and complexity

**17. Limit query depth and complexity.** A pathological query like `{ a { a { a { ... } } } }` 50 levels deep is a DoS.

```ts
// reference: graphql-armor or similar middleware
import { maxDepthRule } from "@escape.tech/graphql-armor-max-depth";
import { costLimitRule } from "@escape.tech/graphql-armor-cost-limit";

const validationRules = [
  maxDepthRule({ n: 10 }),
  costLimitRule({ maxCost: 5000, scalarCost: 1, objectCost: 2, listFactor: 10 }),
];
```

**18. Persisted queries in production.** Clients send a hash; server resolves to a pre-registered query. Defeats arbitrary-query DoS and shrinks request size.

### Auth

**19. Field-level auth checks in resolvers.** Not "validate the JWT in the gateway and trust everything below."

```ts
type Order @auth(requires: ["orders:read"]) {
  id: ID!
  total: Money!
  customer: Customer
  internalNotes: String @auth(requires: ["orders:admin"])  # admin only
}
```

**20. Auth context lives in the request context, not in arguments.** Same rule as MCP: never trust the model/client to pass tenant_id.

### Caching

**21. Cache by query + variables hash.** Persisted queries make this trivial.

**22. `@cacheControl` directive for field-level TTL.**

```graphql
type Product @cacheControl(maxAge: 300) {
  id: ID!
  name: String!
  inventory: Int @cacheControl(maxAge: 30)  # shorter TTL on inventory
}
```

### Subscriptions

**23. Subscriptions for live updates, NOT for one-shot reads.** Use a query.

**24. Subscription payloads are typed like mutation payloads (often unions).**

### Telemetry

**25. Trace at the resolver level.** Each resolver becomes a span. Apollo, Yoga, Mercurius all support this.

**26. Log slow queries.** A 2-second resolver buried inside a `{ orders { items { product { variant { ... } } } } }` is hard to find.

## Common AI-output patterns to reject

| Pattern | Why wrong | Fix |
| --- | --- | --- |
| Every field marked `!` | One null cascades up | Deliberate nullability |
| `orders(page: Int, perPage: Int)` | Offset pagination | Relay Connection (first/after) |
| `throw new Error("not found")` from resolver | Untyped failure | Typed error union in the payload |
| `users.map((u) => getUser(u.id))` | N+1 | DataLoader |
| `String` IDs | Loses graph semantics | `ID` |
| `Float` for money | Rounding | `Money { amountCents, currency }` |
| `Date` as `String` | Format ambiguity | `DateTime` scalar |
| Removing field directly | Breaking change | `@deprecated` first, remove later |
| No max-depth rule | DoS surface | `maxDepthRule({ n: 10 })` |
| Arbitrary queries in production | DoS + cache miss | Persisted queries |
| Auth at gateway only | Field-level leaks | `@auth` directive or resolver checks |
| `subscription { order } ` for one-shot | Wrong primitive | `query` |

## Worked example: a Connection-paginated mutation-payload-union schema

```graphql
"""
Order with deliberate nullability.
A row failure on `customer` should not null the whole order.
"""
type Order {
  id: ID!
  customer: Customer            # nullable
  items: [LineItem!]!           # list non-null, items non-null
  status: OrderStatus!
  total: Money!
  createdAt: DateTime!
  shippedAt: DateTime           # nullable - "not shipped" is a state
}

type LineItem {
  sku: String!
  quantity: Int!
  unitPrice: Money!
}

type Money {
  amountCents: Int!
  currency: Currency!
}

enum Currency { USD EUR KZT }
enum OrderStatus { PENDING PAID FULFILLED CANCELLED REFUNDED }

# Connection pagination per Relay spec
type Query {
  orders(
    first: Int = 50
    after: String
    customerId: ID
    status: OrderStatus
  ): OrderConnection!
}

type OrderConnection {
  edges: [OrderEdge!]!
  pageInfo: PageInfo!
}
type OrderEdge {
  node: Order!
  cursor: String!
}
type PageInfo {
  hasNextPage: Boolean!
  hasPreviousPage: Boolean!
  startCursor: String
  endCursor: String
}

# Mutation: payload union for typed errors
type Mutation {
  createOrder(input: CreateOrderInput!): CreateOrderPayload!
}

input CreateOrderInput {
  customerId: ID!
  items: [LineItemInput!]!
  currency: Currency!
}
input LineItemInput {
  sku: String!
  quantity: Int!
  unitPriceCents: Int!
}

union CreateOrderPayload =
  | CreateOrderSuccess
  | ValidationError
  | CustomerNotFoundError
  | OutOfStockError

type CreateOrderSuccess { order: Order! }
type ValidationError { message: String!  fields: [FieldError!]! }
type FieldError { path: String!  code: String!  message: String! }
type CustomerNotFoundError { customerId: ID! }
type OutOfStockError { sku: String!  available: Int! }

scalar DateTime
```

```ts
// resolver
const resolvers = {
  Mutation: {
    async createOrder(_root, { input }, ctx) {
      const parsed = CreateOrderSchema.safeParse(input);
      if (!parsed.success) {
        return {
          __typename: "ValidationError",
          message: "Validation failed.",
          fields: parsed.error.issues.map((i) => ({ path: i.path.join("."), code: i.code, message: i.message })),
        };
      }

      const customer = await ctx.loaders.customer.load(parsed.data.customerId);
      if (!customer) {
        return { __typename: "CustomerNotFoundError", customerId: parsed.data.customerId };
      }

      for (const item of parsed.data.items) {
        const stock = await ctx.loaders.inventory.load(item.sku);
        if (!stock || stock.available < item.quantity) {
          return { __typename: "OutOfStockError", sku: item.sku, available: stock?.available ?? 0 };
        }
      }

      const order = await ctx.db.orders.create(parsed.data);
      return { __typename: "CreateOrderSuccess", order };
    },
  },

  Order: {
    customer(order, _args, ctx) {
      return ctx.loaders.customer.load(order.customer_id);
    },
  },
};
```

What this demonstrates: deliberate nullability (`customer` nullable, `items` list non-null) (rule 1-2); Connection pagination (rule 4); typed error union in the mutation payload (rule 7); DataLoader-based customer resolver to avoid N+1 (rule 9); scalar `DateTime` (rule 12); `Money` object (rule 13); `ID` not `String` (rule 11).

## Workflow

When designing a GraphQL schema:

1. **List the types in prose first.** Order, Customer, LineItem. Plus their relationships.
2. **Pick nullability deliberately.** Default nullable; mark `!` only on truly-stable fields.
3. **Pick pagination once: Connection spec everywhere.**
4. **Decide on the typed-error-union pattern.** Apply consistently to all mutations that can fail in expected ways.
5. **Identify N+1 risk on every list field. Set up DataLoaders.**
6. **Add depth/complexity limits before going to production.**
7. **Plan for deprecation, not removal.**

## Verification

Manual checklist:

- [ ] Nullability is deliberate, not "everything `!`".
- [ ] Pagination uses Connection spec.
- [ ] Mutations that can fail in expected ways return a typed payload union.
- [ ] Every list field that resolves to N rows has a DataLoader-backed resolver.
- [ ] Custom scalars for IDs, dates, money.
- [ ] Depth and complexity limits enabled.
- [ ] Persisted queries for production clients.
- [ ] No field removed without deprecation period.

## When to skip this skill

- REST APIs (see [`forge-api-design`](../forge-api-design/SKILL.md)).
- Internal service-to-service RPC where gRPC or REST is simpler.
- Prototype schemas that will not survive the demo.

## Related skills

- [`forge-api-design`](../forge-api-design/SKILL.md) - REST counterpart (same discipline, different protocol).
- [`forge-validation`](../forge-validation/SKILL.md) - input validation lives in resolvers.
- [`forge-error-handling`](../forge-error-handling/SKILL.md) - resolver throw vs typed payload split.
- [`forge-auth`](../forge-auth/SKILL.md) - field-level auth.
- [`forge-observability`](../../infra/forge-observability/SKILL.md) - resolver-level tracing.

---
> Source: [f4rkh4d/forge-skill](https://github.com/f4rkh4d/forge-skill) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->

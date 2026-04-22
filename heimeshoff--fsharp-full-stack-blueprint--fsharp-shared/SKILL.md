---
name: fsharp-shared
description: | Use when this capability is needed.
metadata:
  author: heimeshoff
---

# F# Shared Types and API Contracts

## Philosophy: Types as Executable Specification

In F#, types aren't just data containers—they're executable specifications. Well-designed types make illegal states unrepresentable and make the code self-documenting.

**Before defining types, ask:**
- What concepts exist in this domain?
- What states can entities be in?
- What operations are valid for each state?
- What can go wrong? (Model errors as types, not strings)

**Core Principles:**

1. **Make Illegal States Unrepresentable**: If it can't exist in the domain, it shouldn't compile. Use discriminated unions to constrain possibilities.

2. **Types Are Documentation**: A well-designed type tells you what you can do with it. If you need comments to explain, the type could be clearer.

3. **Separate Inputs from Entities**: Create request types separate from domain entities. The API caller shouldn't set server-managed fields.

4. **Option Over Null**: Use `option` for optional values. Null doesn't exist in our vocabulary.

---

## Type Design Patterns

### Records: Structured Data

Records are the foundation. They're immutable by default and have structural equality.

```fsharp
type Order = {
    Id: int
    CustomerId: int
    Items: OrderItem list
    Status: OrderStatus
    CreatedAt: DateTime
    ShippedAt: DateTime option  // option for nullable
}

type OrderItem = {
    ProductId: int
    ProductName: string
    Quantity: int
    UnitPrice: decimal
}
```

**Key decisions:**
- Use `option` for fields that may not have values
- Use `list` for collections (immutable, good for F#)
- Avoid nested nullability—`string option option` is a smell

### Discriminated Unions: State Machines

Unions model states, choices, and constrained sets. The compiler enforces exhaustive handling.

```fsharp
// Simple enumeration
type Priority = Low | Medium | High | Urgent

// State machine
type OrderStatus =
    | Draft
    | Submitted
    | Processing
    | Shipped of trackingNumber: string
    | Delivered of deliveredAt: DateTime
    | Cancelled of reason: string

// Choice type
type PaymentMethod =
    | CreditCard of last4: string * expiry: string
    | BankTransfer of accountNumber: string
    | PayPal of email: string
```

**Why unions?**
- Pattern matching is exhaustive—you can't forget a case
- Adding a case breaks all handlers—the compiler guides updates
- State-specific data is co-located with the state

### Smart Constructors: Constrained Values

For values with invariants (non-empty strings, positive numbers, valid emails), use private types with factory functions.

```fsharp
type EmailAddress = private EmailAddress of string

module EmailAddress =
    let create (s: string) : Result<EmailAddress, string> =
        if s.Contains("@") && s.Length >= 5 then
            Ok (EmailAddress s)
        else
            Error "Invalid email format"

    let value (EmailAddress s) = s

// Usage
match EmailAddress.create input with
| Ok email -> { User with Email = email }  // EmailAddress is guaranteed valid
| Error msg -> // handle invalid input
```

**When to use:**
- Email addresses, phone numbers (format constraints)
- Positive integers, non-empty strings (value constraints)
- Domain identifiers (type safety)

---

## API Contract Patterns

### Basic CRUD Interface

```fsharp
type IOrderApi = {
    // Queries: always return data (empty list if none)
    getAll: unit -> Async<Order list>
    getByCustomer: int -> Async<Order list>

    // Single item: may not exist
    getById: int -> Async<Result<Order, string>>

    // Commands: may fail
    create: CreateOrderRequest -> Async<Result<Order, string>>
    update: int * UpdateOrderRequest -> Async<Result<Order, string>>
    delete: int -> Async<Result<unit, string>>
}
```

### Return Type Guide

| Scenario | Return Type | Example |
|----------|-------------|---------|
| Collection query | `Async<'T list>` | `getAll: unit -> Async<Order list>` |
| Single item lookup | `Async<Result<'T, string>>` | `getById: int -> Async<Result<Order, string>>` |
| Create/Update | `Async<Result<'T, string>>` | `create: Request -> Async<Result<Order, string>>` |
| Delete | `Async<Result<unit, string>>` | `delete: int -> Async<Result<unit, string>>` |
| Void operation | `Async<unit>` | Rare—prefer Result for traceability |

**Philosophy**: Queries that return collections succeed with empty list. Operations that can fail return Result.

### Request Types: Separate from Entities

```fsharp
// Don't make caller set Id, CreatedAt, etc.
type CreateOrderRequest = {
    CustomerId: int
    Items: CreateOrderItemRequest list
    Notes: string option
}

type CreateOrderItemRequest = {
    ProductId: int
    Quantity: int
}

// For partial updates, use option fields
type UpdateOrderRequest = {
    Notes: string option option  // None = don't change, Some None = clear, Some (Some x) = set
    Status: OrderStatus option   // None = don't change
}
```

**Alternative: Explicit update types**
```fsharp
type UpdateOrderRequest =
    | UpdateNotes of string option
    | UpdateStatus of OrderStatus
    | UpdateMultiple of notes: string option * status: OrderStatus
```

---

## Common Domain Patterns

### Timestamps

```fsharp
type Auditable = {
    CreatedAt: DateTime
    UpdatedAt: DateTime
    CreatedBy: string option
    UpdatedBy: string option
}
```

### Soft Delete

```fsharp
type Deletable = {
    IsDeleted: bool
    DeletedAt: DateTime option
}

// Or union approach (makes illegal states impossible)
type EntityState =
    | Active
    | Deleted of deletedAt: DateTime * deletedBy: string
```

### Pagination

```fsharp
type PageRequest = {
    Page: int
    PageSize: int
    SortBy: string option
    SortDirection: SortDirection option
}

type SortDirection = Ascending | Descending

type PagedResult<'T> = {
    Items: 'T list
    TotalCount: int
    Page: int
    PageSize: int
    TotalPages: int
}

// API
type IProductApi = {
    search: string * PageRequest -> Async<PagedResult<Product>>
}
```

### Error Types (Beyond Strings)

```fsharp
type OrderError =
    | OrderNotFound
    | InvalidQuantity of min: int * max: int
    | OutOfStock of productId: int
    | PaymentFailed of reason: string
    | ShippingUnavailable of region: string

type IOrderApi = {
    create: CreateOrderRequest -> Async<Result<Order, OrderError>>
}
```

**When to use typed errors:**
- Client needs to handle different errors differently
- Domain has specific failure modes worth modeling
- You want exhaustive error handling

---

## Anti-Patterns to Avoid

❌ **Classes for Domain Types**
```fsharp
// BAD
type Order() =
    member val Id = 0 with get, set
    member val Items = [] with get, set
```
*Why bad*: Mutable, no structural equality, verbose.
*Better*: Use records.

❌ **Null Instead of Option**
```fsharp
// BAD
type User = { Email: string; Phone: string }  // null for no phone?
```
*Why bad*: Null is not an F# idiom. It causes NullReferenceExceptions.
*Better*: `Phone: string option`

❌ **Stringly Typed Data**
```fsharp
// BAD
type Order = { Status: string }  // "pending", "shipped", etc.
```
*Why bad*: Typos compile, no exhaustive matching, no type safety.
*Better*: `Status: OrderStatus` with a union.

❌ **Logic in Type Definitions**
```fsharp
// BAD
type Order = {
    Items: OrderItem list
    member this.Total = this.Items |> List.sumBy (fun i -> i.Price * decimal i.Qty)
}
```
*Why bad*: Types should be data. Logic goes in modules.
*Better*: `module Order = let total order = ...`

❌ **God Types**
```fsharp
// BAD: One type for all scenarios
type Order = {
    Id: int
    DraftItems: OrderItem list option       // for drafts
    SubmittedAt: DateTime option            // for submitted
    ShippedTrackingNumber: string option    // for shipped
    DeliveredAt: DateTime option            // for delivered
}
```
*Why bad*: Most fields optional, invariants not enforced.
*Better*: Union of different order stages, each with its data.

---

## Variation Guidance

**Simple CRUD entities**: Straightforward records, basic union for status.

**Complex workflows**: State machine unions with stage-specific data.

**External integrations**: DTOs that mirror external API shapes, separate from domain types.

**Event sourcing**: Event types as unions, state rebuilt from events.

The type design matches domain complexity. Don't over-engineer simple data; don't under-model complex state.

---

## Type Design Checklist

Before finalizing types, verify:

- [ ] Records used for data, unions for choices/states
- [ ] `option` used for nullable fields (no nulls)
- [ ] Request types separate from entities
- [ ] Server-managed fields not in request types
- [ ] Meaningful names (not `Status`, `Type`, `Data`)
- [ ] Collection fields use `list` (not `array` unless needed)
- [ ] No logic in type definitions
- [ ] No impossible states representable

---

## Remember

Types are the foundation. Every other layer—validation, domain logic, persistence, API, frontend—builds on types. Time spent on good type design pays dividends throughout the stack.

**The goal**: When you read the types, you understand the domain. When you compile, impossible states are impossible.

## Related Documentation

- `/docs/04-SHARED-TYPES.md` - Detailed type design patterns
- `/docs/09-QUICK-REFERENCE.md` - Quick templates

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/heimeshoff) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

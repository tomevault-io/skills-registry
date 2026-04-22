---
name: fsharp-validation
description: | Use when this capability is needed.
metadata:
  author: heimeshoff
---

# F# Validation Patterns

## Philosophy: Guard the Gate Once

Validation is a gatekeeper—it ensures invalid data never reaches your domain logic. Once data passes validation, it's trusted throughout the system. This "trust but verify once" pattern keeps domain logic clean.

**Before writing validators, ask:**
- What makes this input invalid?
- Should I collect all errors or fail fast?
- Is this validation about format, business rules, or external state?
- Where is the single point where this validation should happen?

**Core Principles:**

1. **Validate at the Boundary**: Validation happens where data enters the system—at the API layer. Not deep inside, not multiple places.

2. **Accumulate All Errors**: Users hate submitting a form five times to find five errors. Collect and return all validation errors at once.

3. **Errors Are Data**: Validation errors are expected outcomes, not exceptions. Return `Result<'T, Error>`, not throw.

4. **Business Rules Belong in Domain**: Validation checks format and presence. Complex business rules may belong in the Domain layer, returning Results.

---

## Validator Architecture

```
Input
  ↓
Individual Validators (return Option<string>)
  ↓
Entity Validator (collects errors, returns Result)
  ↓
API Layer (unwraps Result, handles errors)
```

**The flow**: Small validators compose into entity validators, which return structured results.

---

## Individual Validators

Each validator checks one thing and returns `Option<string>`: `None` means valid, `Some errorMessage` means invalid.

```fsharp
module Validation

open System
open System.Text.RegularExpressions

// Required fields
let validateRequired (field: string) (value: string) : string option =
    if String.IsNullOrWhiteSpace(value) then
        Some $"{field} is required"
    else
        None

// Length constraints
let validateMinLength (field: string) (min: int) (value: string) : string option =
    if value.Length < min then
        Some $"{field} must be at least {min} characters"
    else
        None

let validateMaxLength (field: string) (max: int) (value: string) : string option =
    if value.Length > max then
        Some $"{field} must be at most {max} characters"
    else
        None

let validateLength (field: string) (min: int) (max: int) (value: string) : string option =
    if value.Length < min then Some $"{field} must be at least {min} characters"
    elif value.Length > max then Some $"{field} must be at most {max} characters"
    else None

// Numeric constraints
let validateRange (field: string) (min: int) (max: int) (value: int) : string option =
    if value < min || value > max then
        Some $"{field} must be between {min} and {max}"
    else
        None

let validatePositive (field: string) (value: int) : string option =
    if value <= 0 then Some $"{field} must be positive" else None

let validateNonNegative (field: string) (value: decimal) : string option =
    if value < 0m then Some $"{field} cannot be negative" else None

// Format validation
let validateEmail (email: string) : string option =
    let pattern = @"^[^@\s]+@[^@\s]+\.[^@\s]+$"
    if Regex.IsMatch(email, pattern) then None
    else Some "Invalid email format"

let validateUrl (url: string) : string option =
    match Uri.TryCreate(url, UriKind.Absolute) with
    | true, _ -> None
    | false, _ -> Some "Invalid URL format"

let validatePattern (field: string) (pattern: string) (value: string) : string option =
    if Regex.IsMatch(value, pattern) then None
    else Some $"{field} has invalid format"

// Date validation
let validateFutureDate (field: string) (date: DateTime) : string option =
    if date > DateTime.UtcNow then None
    else Some $"{field} must be in the future"

let validatePastDate (field: string) (date: DateTime) : string option =
    if date < DateTime.UtcNow then None
    else Some $"{field} must be in the past"
```

**Why this pattern?**
- Each validator is simple and testable
- Composable—combine validators freely
- Clear error messages—each validator knows its context

---

## Entity Validators

Compose individual validators into entity-level validation that accumulates all errors.

```fsharp
let validateCreateOrder (request: CreateOrderRequest) : Result<CreateOrderRequest, string list> =
    let errors = [
        // Required fields
        validateRequired "Customer ID" (string request.CustomerId)

        // Conditional validation
        if request.Items.IsEmpty then
            Some "Order must have at least one item"

        // Validate nested items
        for i, item in request.Items |> List.indexed do
            validatePositive $"Item {i+1} quantity" item.Quantity
            validateNonNegative $"Item {i+1} price" item.UnitPrice

        // Cross-field validation
        match request.ShippingDate, request.RequiredBy with
        | Some ship, Some required when ship > required ->
            Some "Shipping date cannot be after required date"
        | _ -> None

    ] |> List.choose id

    if errors.IsEmpty then Ok request else Error errors
```

**Patterns:**
- Use list comprehension with `List.choose id` to collect errors
- Validate nested collections with indexed loops
- Handle cross-field validation in the same block

---

## Validation Scenarios

### Optional Fields

```fsharp
let validateOptionalDescription (desc: string option) : string option =
    match desc with
    | None -> None  // Optional, so absence is valid
    | Some d when String.IsNullOrWhiteSpace(d) -> Some "Description cannot be empty if provided"
    | Some d when d.Length > 1000 -> Some "Description too long"
    | Some _ -> None
```

### Conditional Validation

```fsharp
let validateUser (user: UserRequest) : Result<UserRequest, string list> =
    let errors = [
        validateRequired "Name" user.Name
        validateEmail user.Email

        // Only validate password if changing it
        if user.IsChangingPassword then
            validateMinLength "Password" 8 user.Password
            if not (Regex.IsMatch(user.Password, "[A-Z]")) then
                Some "Password must contain uppercase letter"
            if not (Regex.IsMatch(user.Password, "[0-9]")) then
                Some "Password must contain digit"

        // Role-specific validation
        match user.Role with
        | Admin -> validateRequired "Admin code" user.AdminCode
        | _ -> None

    ] |> List.choose id

    if errors.IsEmpty then Ok user else Error errors
```

### Business Rules

Some validation is really business logic. Keep it close to domain.

```fsharp
// Validation.fs - format/presence checks
let validateOrderRequest (req: CreateOrderRequest) =
    // ... basic validation

// Domain.fs - business rules
let canCreateOrder (customer: Customer) (order: Order) : Result<Order, string> =
    if customer.IsBlocked then
        Error "Customer account is blocked"
    elif order.Total > customer.CreditLimit then
        Error "Order exceeds credit limit"
    elif order.Items |> List.exists (fun i -> i.Quantity > 100) then
        Error "Single item quantity exceeds maximum"
    else
        Ok order
```

**The distinction**: Validation checks that data is well-formed. Business rules check that the operation makes sense given system state.

---

## Async Validation

For validations requiring database lookups, use async validators.

```fsharp
let checkEmailUnique (email: string) : Async<string option> =
    async {
        let! existing = Persistence.getUserByEmail email
        return
            match existing with
            | Some _ -> Some "Email already registered"
            | None -> None
    }

let checkInventoryAvailable (productId: int) (quantity: int) : Async<string option> =
    async {
        let! stock = Persistence.getStock productId
        return
            if stock >= quantity then None
            else Some $"Only {stock} items available"
    }

// Compose sync and async validation
let validateRegistration (req: RegisterRequest) : Async<Result<RegisterRequest, string list>> =
    async {
        // Run sync validations first (fast fail)
        let syncErrors = [
            validateRequired "Username" req.Username
            validateLength "Username" 3 20 req.Username
            validateEmail req.Email
            validateMinLength "Password" 8 req.Password
        ] |> List.choose id

        if not syncErrors.IsEmpty then
            return Error syncErrors
        else
            // Async validations only if sync passed
            let! emailCheck = checkEmailUnique req.Email
            let! usernameCheck = checkUsernameUnique req.Username

            let asyncErrors = [ emailCheck; usernameCheck ] |> List.choose id

            if asyncErrors.IsEmpty then
                return Ok req
            else
                return Error asyncErrors
    }
```

**Performance tip**: Run sync validation first. Only hit the database if basic checks pass.

---

## Integration with API

```fsharp
// Api.fs
let orderApi : IOrderApi = {
    create = fun request -> async {
        // 1. Validate request
        match Validation.validateCreateOrder request with
        | Error errors ->
            return Error (String.concat "; " errors)
        | Ok validRequest ->
            // 2. Business rule check
            let! customer = Persistence.getCustomer validRequest.CustomerId
            match Domain.canCreateOrder customer (Domain.createOrder validRequest) with
            | Error reason ->
                return Error reason
            | Ok order ->
                // 3. Persist
                let! saved = Persistence.insertOrder order
                return Ok saved
    }

    // For endpoints requiring async validation
    register = fun request -> async {
        match! Validation.validateRegistration request with
        | Error errors ->
            return Error (String.concat "; " errors)
        | Ok validRequest ->
            let! user = Domain.createUser validRequest |> Persistence.insertUser
            return Ok user
    }
}
```

---

## Anti-Patterns to Avoid

❌ **Validating Multiple Times**
```fsharp
// BAD: Checking in API and again in Domain
let create request =
    match validate request with  // First time
    | Ok r ->
        let entity = Domain.create r  // Domain also validates?
```
*Why bad*: Wasted cycles, inconsistent error handling.
*Better*: Validate once at boundary, trust data downstream.

❌ **Using Exceptions for Validation**
```fsharp
// BAD
let validateAge age =
    if age < 0 then raise (ValidationException "Invalid age")
```
*Why bad*: Control flow via exceptions is expensive and unclear.
*Better*: Return `Result` or `Option`.

❌ **Failing on First Error**
```fsharp
// BAD
let validate request =
    if String.IsNullOrEmpty request.Title then Error "Title required"
    elif request.Title.Length > 100 then Error "Title too long"
    elif String.IsNullOrEmpty request.Email then Error "Email required"
    // User has to submit 3 times to find 3 errors
```
*Why bad*: Frustrating UX.
*Better*: Accumulate all errors.

❌ **Generic Error Messages**
```fsharp
// BAD
Some "Invalid input"
Some "Validation failed"
```
*Why bad*: User doesn't know what to fix.
*Better*: Specific messages mentioning the field and constraint.

❌ **Validation Logic in Persistence**
```fsharp
// BAD: Database layer doing validation
let insert entity =
    if String.IsNullOrEmpty entity.Name then
        failwith "Name required"  // Wrong place!
```
*Why bad*: Too late, wrong layer.
*Better*: Validate before reaching persistence.

---

## Variation Guidance

**Simple forms**: Just required fields and basic format checks.

**Complex entities**: Cross-field validation, conditional rules, business constraints.

**External integrations**: Async validation for uniqueness, inventory, rate limits.

**High-security**: Multiple validation layers, but still single source of truth for each rule.

Match complexity to the domain. Simple data needs simple validation.

---

## Testing Validators

```fsharp
[<Tests>]
let validationTests =
    testList "Validation" [
        testCase "valid request passes" <| fun () ->
            let request = { Title = "Valid"; Description = None }
            Expect.isOk (validateRequest request) "Should pass"

        testCase "empty title fails" <| fun () ->
            let request = { Title = ""; Description = None }
            Expect.isError (validateRequest request) "Should fail"

        testCase "accumulates multiple errors" <| fun () ->
            let request = { Title = ""; Email = "invalid" }
            match validateRequest request with
            | Error errors -> Expect.isGreaterThan errors.Length 1 "Multiple errors"
            | Ok _ -> failtest "Should fail"

        testCase "specific error messages" <| fun () ->
            let request = { Title = "" }
            match validateRequest request with
            | Error errors ->
                Expect.exists errors (fun e -> e.Contains("Title")) "Should mention field"
            | Ok _ -> failtest "Should fail"
    ]
```

---

## Remember

Validation is your first line of defense. Good validation catches problems early, provides clear feedback, and keeps your domain logic clean. Validate once, validate well, and trust the result.

**The goal**: Invalid data never reaches your domain. Users know exactly what to fix. Errors are data, not surprises.

## Related Documentation

- `/docs/03-BACKEND-GUIDE.md` - Backend patterns including validation
- `/docs/09-QUICK-REFERENCE.md` - Code templates

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/heimeshoff) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

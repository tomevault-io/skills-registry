---
name: fsharp-backend
description: | Use when this capability is needed.
metadata:
  author: heimeshoff
---

# F# Backend Implementation

## Philosophy: Purity at the Core

The backend is structured like an onion—pure domain logic at the center, I/O at the edges.

**Before implementing, ask:**
- What is the core business logic that should be pure?
- What are the boundaries where validation should occur?
- What side effects (I/O) are truly necessary?
- How will errors flow back to the caller?

**Core Principles:**

1. **Purity Enables Testability**: Pure functions are trivial to test. If you find domain logic hard to test, it probably contains hidden I/O.

2. **Validate Once at the Edge**: Validation happens at API boundaries. After validation, data is trusted—no defensive re-checking deep in the code.

3. **Errors Are Data, Not Exceptions**: Use `Result<'T, Error>` for expected failures. Exceptions are for unexpected, unrecoverable situations.

4. **Orchestration Lives in the API Layer**: The API layer is the "conductor"—it calls validation, domain, and persistence, but contains no business logic itself.

## Architecture

```
API (Fable.Remoting) ← Orchestrates everything
    ↓
Validation          ← Guards the gate
    ↓
Domain              ← Pure business logic (the heart)
    ↓
Persistence         ← I/O operations
```

**The mental model**: Data flows in through validation, gets transformed by pure domain functions, and results are persisted or returned. Each layer has exactly one responsibility.

---

## Layer 1: Validation (`src/Server/Validation.fs`)

**Purpose:** Guard the API boundary. Invalid data never reaches domain logic.

### Thinking Framework

Before writing validators, ask:
- What makes this input invalid? (required fields, formats, ranges)
- Should I accumulate all errors or fail fast?
- Are there cross-field validations (e.g., end date after start date)?

### Pattern: Accumulating Validators

```fsharp
module Validation

open System

// Individual validators return Option<string>
// None = valid, Some = error message
let validateRequired (field: string) (value: string) =
    if String.IsNullOrWhiteSpace(value) then Some $"{field} is required"
    else None

let validateLength (field: string) (min: int) (max: int) (value: string) =
    if value.Length < min then Some $"{field} must be at least {min} characters"
    elif value.Length > max then Some $"{field} must be at most {max} characters"
    else None

// Entity validators accumulate all errors
let validateEntity (entity: Entity) : Result<Entity, string list> =
    let errors = [
        validateRequired "Name" entity.Name
        validateLength "Name" 1 100 entity.Name
        // Add more validators...
    ] |> List.choose id

    if errors.IsEmpty then Ok entity else Error errors
```

**Key insight**: Returning `Option<string>` from individual validators and `List.choose id` to collect them is idiomatic F#. The pattern is composable and extensible.

---

## Layer 2: Domain (`src/Server/Domain.fs`)

**Purpose:** Pure business logic. No I/O. No side effects. Just data transformations.

### The Purity Test

Ask yourself: "Can I test this function with just inputs and outputs, no mocking?"

If the answer is no, you have I/O in your domain—extract it.

### Pattern: Pure Transformations

```fsharp
module Domain

open System
open Shared.Domain

// ✅ PURE: Takes data, returns transformed data
let createEntity (request: CreateRequest) : Entity =
    {
        Id = 0  // Set by persistence
        Name = request.Name.Trim()
        Status = Active
        CreatedAt = DateTime.UtcNow
    }

// ✅ PURE: State transition
let complete (entity: Entity) : Entity =
    { entity with Status = Completed; UpdatedAt = DateTime.UtcNow }

// ✅ PURE: Business calculation
let calculateTotal (items: LineItem list) : decimal =
    items |> List.sumBy (fun i -> i.Price * decimal i.Quantity)
```

### Anti-Pattern: I/O in Domain

```fsharp
// ❌ BAD: Domain function with I/O
let processEntity id =
    let entity = Persistence.getById id  // Database call!
    { entity with Status = Processed }

// ✅ GOOD: Pure transformation, I/O handled elsewhere
let processEntity entity =
    { entity with Status = Processed }
```

**Why this matters**: The bad version is untestable without mocking the database. The good version tests with a simple `entity |> processEntity |> (fun e -> e.Status = Processed)`.

---

## Layer 3: Persistence (`src/Server/Persistence.fs`)

**Purpose:** All I/O operations. Database queries, file operations, external calls.

### Pattern: Async Everything

```fsharp
module Persistence

open Dapper
open Microsoft.Data.Sqlite

let connectionString = "Data Source=./data/app.db"
let getConnection () = new SqliteConnection(connectionString)

let getAll () : Async<Entity list> =
    async {
        use conn = getConnection()
        let! results = conn.QueryAsync<Entity>(
            "SELECT * FROM Entities ORDER BY CreatedAt DESC"
        ) |> Async.AwaitTask
        return results |> Seq.toList
    }

let getById (id: int) : Async<Entity option> =
    async {
        use conn = getConnection()
        let! result = conn.QuerySingleOrDefaultAsync<Entity>(
            "SELECT * FROM Entities WHERE Id = @Id",
            {| Id = id |}
        ) |> Async.AwaitTask
        return if isNull (box result) then None else Some result
    }

let insert (entity: Entity) : Async<Entity> =
    async {
        use conn = getConnection()
        let! id = conn.ExecuteScalarAsync<int64>(
            """INSERT INTO Entities (Name, Status, CreatedAt)
               VALUES (@Name, @Status, @CreatedAt)
               RETURNING Id""",
            entity
        ) |> Async.AwaitTask
        return { entity with Id = int id }
    }
```

**Key points:**
- `use` ensures connections are disposed
- Always use parameterized queries (SQL injection prevention)
- Return `option` for queries that may not find results

---

## Layer 4: API (`src/Server/Api.fs`)

**Purpose:** Orchestrate the other layers. No business logic here—just coordination.

### Pattern: Orchestration Flow

```fsharp
module Api

open Fable.Remoting.Server
open Fable.Remoting.Giraffe
open Shared.Api

let api : IEntityApi = {
    getAll = fun () -> Persistence.getAll()

    getById = fun id -> async {
        match! Persistence.getById id with
        | Some entity -> return Ok entity
        | None -> return Error "Entity not found"
    }

    create = fun request -> async {
        // 1. Validate at boundary
        match Validation.validateRequest request with
        | Error errors -> return Error (String.concat "; " errors)
        | Ok validRequest ->
            // 2. Transform with domain (pure)
            let entity = Domain.createEntity validRequest
            // 3. Persist
            let! saved = Persistence.insert entity
            return Ok saved
    }

    update = fun id updates -> async {
        // Validate
        match Validation.validateUpdates updates with
        | Error errors -> return Error (String.concat "; " errors)
        | Ok validUpdates ->
            // Check exists
            match! Persistence.getById id with
            | None -> return Error "Entity not found"
            | Some existing ->
                // Transform (pure)
                let updated = Domain.applyUpdates existing validUpdates
                // Persist
                do! Persistence.update updated
                return Ok updated
    }
}

let webApp =
    Remoting.createApi()
    |> Remoting.fromValue api
    |> Remoting.buildHttpHandler
```

**The pattern**: Validate → Transform → Persist → Return. Each step is clear and single-purpose.

---

## Anti-Patterns to Avoid

❌ **I/O in Domain**
```fsharp
// Domain.fs
let process id =
    let item = Persistence.get id  // NO!
```
*Why bad*: Makes domain untestable, couples pure logic to infrastructure.
*Better*: Accept the item as a parameter, let API layer fetch it.

❌ **Business Logic in API**
```fsharp
// Api.fs
create = fun request -> async {
    let trimmed = request.Name.Trim()  // Should be in Domain
    let status = if request.Priority > 3 then Urgent else Normal  // Should be in Domain
```
*Why bad*: Business rules scattered across layers, hard to test.
*Better*: Move all transformations to Domain layer.

❌ **Swallowing Errors**
```fsharp
create = fun request -> async {
    try
        let! result = doSomething()
        return Ok result
    with ex ->
        printfn "Error: %s" ex.Message
        return Ok defaultValue  // Hiding the error!
```
*Why bad*: Caller can't distinguish success from failure.
*Better*: Return `Error ex.Message` so client knows something went wrong.

❌ **Skipping Validation**
```fsharp
create = fun request -> async {
    let entity = Domain.create request  // No validation!
    let! saved = Persistence.insert entity
```
*Why bad*: Invalid data reaches persistence, causes cryptic errors.
*Better*: Always validate at API boundary first.

---

## Variation Guidance

**Adapt these patterns to your context:**

- **Simple CRUD**: Validation and Domain layers might be minimal—that's fine
- **Complex business logic**: Domain layer grows, consider sub-modules
- **Multiple data sources**: Persistence might wrap multiple repositories
- **Event sourcing**: Persistence appends events, Domain rebuilds state

The layer structure is constant; the content of each layer varies with complexity.

---

## Remember

Backend code lives forever. The patterns here—purity at the core, I/O at the edges, validation at the boundaries—create code that's testable, maintainable, and comprehensible years later.

**The goal**: Anyone reading your backend code should immediately understand what each layer does and where to find any given behavior.

## Related Documentation

- `/docs/03-BACKEND-GUIDE.md` - Detailed backend patterns
- `/docs/05-PERSISTENCE.md` - Database and file persistence
- `/docs/09-QUICK-REFERENCE.md` - Code templates

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/heimeshoff) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

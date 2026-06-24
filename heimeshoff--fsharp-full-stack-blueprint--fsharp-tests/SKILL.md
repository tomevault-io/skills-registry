---
name: fsharp-tests
description: | Use when this capability is needed.
metadata:
  author: heimeshoff
---

# F# Testing with Expecto

## Philosophy: Test What Matters

Tests are not bureaucratic overhead—they're executable specifications that catch regressions and document behavior. Focus on testing the parts that matter: domain logic, validation, and state transitions.

**Before writing tests, ask:**
- What behavior am I verifying?
- Is this testing a pure function (easy) or I/O (harder)?
- What edge cases exist?
- What's the cost if this breaks in production?

**Core Principles:**

1. **Test Pure Functions Thoroughly**: Domain logic is pure and trivial to test. No mocking needed. These tests are your best ROI.

2. **Test Boundaries, Not Internals**: Validate inputs at API boundaries, not deep inside implementation. Test the contract, not the implementation.

3. **Property Tests for Invariants**: When a property should always hold (commutativity, idempotence), property-based testing finds edge cases you won't think of.

4. **Fast Tests Enable TDD**: If tests are slow, you won't run them. Keep the fast path fast (pure functions), isolate slow tests (I/O).

---

## Test Project Setup

```
src/Tests/
├── DomainTests.fs        # Pure business logic
├── ValidationTests.fs    # Input validation
├── StateTests.fs         # Elmish state transitions
├── PersistenceTests.fs   # Database/file I/O (integration)
├── Program.fs            # Test runner entry
└── Tests.fsproj
```

### Program.fs

```fsharp
module Program

open Expecto

[<EntryPoint>]
let main args =
    runTestsInAssembly defaultConfig args
```

### Project File

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <GenerateProgramFile>false</GenerateProgramFile>
  </PropertyGroup>

  <ItemGroup>
    <Compile Include="DomainTests.fs" />
    <Compile Include="ValidationTests.fs" />
    <Compile Include="StateTests.fs" />
    <Compile Include="Program.fs" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Expecto" Version="10.2.1" />
    <PackageReference Include="Expecto.FsCheck" Version="10.2.1" />
  </ItemGroup>

  <ItemGroup>
    <ProjectReference Include="../Server/Server.fsproj" />
    <ProjectReference Include="../Shared/Shared.fsproj" />
  </ItemGroup>
</Project>
```

---

## Testing Pure Domain Logic

Pure functions are the easiest and most valuable to test. No setup, no mocking, just input → output.

```fsharp
module DomainTests

open Expecto
open Shared.Domain

// Test fixtures - reusable test data
module TestData =
    let baseOrder = {
        Id = 1
        CustomerId = 100
        Items = []
        Status = Draft
        CreatedAt = DateTime(2024, 1, 1)
    }

    let sampleItem = {
        ProductId = 1
        ProductName = "Widget"
        Quantity = 2
        UnitPrice = 10.0m
    }

[<Tests>]
let domainTests =
    testList "Domain Logic" [
        testList "Order Creation" [
            testCase "new order starts as Draft" <| fun () ->
                let request = { CustomerId = 1; Items = []; Notes = None }
                let order = Domain.createOrder request
                Expect.equal order.Status Draft "Should start as draft"

            testCase "order creation trims customer notes" <| fun () ->
                let request = { CustomerId = 1; Items = []; Notes = Some "  hello  " }
                let order = Domain.createOrder request
                Expect.equal order.Notes (Some "hello") "Should trim notes"
        ]

        testList "Order Calculations" [
            testCase "total is sum of item prices times quantities" <| fun () ->
                let items = [
                    { ProductId = 1; ProductName = "A"; Quantity = 2; UnitPrice = 10.0m }
                    { ProductId = 2; ProductName = "B"; Quantity = 3; UnitPrice = 5.0m }
                ]
                let total = Domain.calculateTotal items
                Expect.equal total 35.0m "2*10 + 3*5 = 35"

            testCase "empty order has zero total" <| fun () ->
                let total = Domain.calculateTotal []
                Expect.equal total 0m "Empty order = $0"
        ]

        testList "Status Transitions" [
            testCase "can submit draft order" <| fun () ->
                let draft = { TestData.baseOrder with Status = Draft }
                let result = Domain.submitOrder draft
                Expect.isOk result "Should succeed"
                match result with
                | Ok order -> Expect.equal order.Status Submitted "Should be submitted"
                | Error _ -> failtest "Expected Ok"

            testCase "cannot submit already submitted order" <| fun () ->
                let submitted = { TestData.baseOrder with Status = Submitted }
                let result = Domain.submitOrder submitted
                Expect.isError result "Should fail"

            testCase "cannot ship unsubmitted order" <| fun () ->
                let draft = { TestData.baseOrder with Status = Draft }
                let result = Domain.shipOrder draft "TRACK123"
                Expect.isError result "Should fail"
        ]
    ]
```

**Key patterns:**
- Group related tests with nested `testList`
- Use test fixtures for reusable data
- Test both success and failure paths
- Use descriptive test names that explain expected behavior

---

## Testing Validation

Validation tests verify boundary conditions and error messages.

```fsharp
module ValidationTests

open Expecto
open Validation

[<Tests>]
let validationTests =
    testList "Validation" [
        testList "Required Fields" [
            testCase "empty title fails" <| fun () ->
                let request = { Title = ""; Description = None }
                let result = Validation.validateRequest request
                Expect.isError result "Should reject empty title"

            testCase "whitespace-only title fails" <| fun () ->
                let request = { Title = "   "; Description = None }
                let result = Validation.validateRequest request
                Expect.isError result "Should reject whitespace"

            testCase "valid title passes" <| fun () ->
                let request = { Title = "Valid Title"; Description = None }
                let result = Validation.validateRequest request
                Expect.isOk result "Should accept valid title"
        ]

        testList "Length Constraints" [
            testCase "title too long fails" <| fun () ->
                let longTitle = String.replicate 201 "a"
                let request = { Title = longTitle; Description = None }
                let result = Validation.validateRequest request
                Expect.isError result "Should reject >200 chars"

            testCase "title at max length passes" <| fun () ->
                let maxTitle = String.replicate 200 "a"
                let request = { Title = maxTitle; Description = None }
                let result = Validation.validateRequest request
                Expect.isOk result "Should accept exactly 200 chars"
        ]

        testList "Error Accumulation" [
            testCase "multiple errors are collected" <| fun () ->
                let request = { Title = ""; Email = "invalid"; Age = -5 }
                match Validation.validateRequest request with
                | Error errors ->
                    Expect.isGreaterThan errors.Length 1 "Should have multiple errors"
                | Ok _ ->
                    failtest "Should have failed"

            testCase "all error messages are specific" <| fun () ->
                let request = { Title = ""; Email = "bad" }
                match Validation.validateRequest request with
                | Error errors ->
                    Expect.exists errors (fun e -> e.Contains("Title")) "Should mention title"
                    Expect.exists errors (fun e -> e.Contains("email")) "Should mention email"
                | Ok _ ->
                    failtest "Should have failed"
        ]
    ]
```

---

## Testing State Transitions (Elmish)

Test the `update` function as a pure state machine.

```fsharp
module StateTests

open Expecto
open State
open Types
open Shared.Domain

module TestModels =
    let initial = {
        Items = NotAsked
        SelectedItem = NotAsked
        FormTitle = ""
        IsSubmitting = false
    }

    let loading = { initial with Items = Loading }
    let withItems items = { initial with Items = Success items }

[<Tests>]
let stateTests =
    testList "State Management" [
        testList "Loading States" [
            testCase "LoadItems sets Loading state" <| fun () ->
                let model, _ = update LoadItems TestModels.initial
                Expect.equal model.Items Loading "Should be Loading"

            testCase "LoadItems returns fetch command" <| fun () ->
                let _, cmd = update LoadItems TestModels.initial
                Expect.isFalse (Cmd.isEmpty cmd) "Should have a command"
        ]

        testList "Data Loaded" [
            testCase "ItemsLoaded Ok sets Success" <| fun () ->
                let items = [{ Id = 1; Title = "Test" }]
                let model, _ = update (ItemsLoaded (Ok items)) TestModels.loading
                Expect.equal model.Items (Success items) "Should be Success"

            testCase "ItemsLoaded Error sets Failure" <| fun () ->
                let model, _ = update (ItemsLoaded (Error "Network error")) TestModels.loading
                match model.Items with
                | Failure msg -> Expect.stringContains msg "Network" "Should have error"
                | _ -> failtest "Should be Failure"
        ]

        testList "Form Submission" [
            testCase "SubmitForm sets IsSubmitting" <| fun () ->
                let model = { TestModels.initial with FormTitle = "Test" }
                let result, _ = update SubmitForm model
                Expect.isTrue result.IsSubmitting "Should be submitting"

            testCase "FormSubmitted Ok clears form" <| fun () ->
                let model = { TestModels.initial with FormTitle = "Test"; IsSubmitting = true }
                let result, _ = update (FormSubmitted (Ok { Id = 1; Title = "Test" })) model
                Expect.equal result.FormTitle "" "Should clear title"
                Expect.isFalse result.IsSubmitting "Should stop submitting"

            testCase "FormSubmitted Ok triggers reload" <| fun () ->
                let model = { TestModels.initial with IsSubmitting = true }
                let _, cmd = update (FormSubmitted (Ok { Id = 1; Title = "Test" })) model
                Expect.isFalse (Cmd.isEmpty cmd) "Should have reload command"
        ]

        testList "Form Updates" [
            testCase "UpdateTitle updates model" <| fun () ->
                let model, _ = update (UpdateTitle "New Title") TestModels.initial
                Expect.equal model.FormTitle "New Title" "Should update title"

            testCase "UpdateTitle returns no command" <| fun () ->
                let _, cmd = update (UpdateTitle "New Title") TestModels.initial
                Expect.isTrue (Cmd.isEmpty cmd) "Should have no command"
        ]
    ]

// Helper to check if command is empty
module Cmd =
    let isEmpty cmd = List.isEmpty cmd
```

---

## Property-Based Testing

For invariants that should always hold, let FsCheck generate test cases.

```fsharp
open FsCheck

[<Tests>]
let propertyTests =
    testList "Properties" [
        testProperty "order total is never negative" <| fun (quantities: int list) (prices: decimal list) ->
            let items =
                List.zip quantities prices
                |> List.map (fun (q, p) -> { Quantity = abs q; UnitPrice = abs p; ProductId = 1; ProductName = "" })
            Domain.calculateTotal items >= 0m

        testProperty "trimming is idempotent" <| fun (s: string) ->
            let trimmed = s.Trim()
            trimmed.Trim() = trimmed

        testProperty "validation passes for all valid inputs" <| fun (NonEmptyString title) ->
            let shortTitle = title.Substring(0, min title.Length 200)
            let request = { Title = shortTitle; Description = None }
            Result.isOk (Validation.validateRequest request)
    ]
```

**When to use property tests:**
- Mathematical properties (commutativity, associativity)
- Invariants (always positive, never empty)
- Round-trip properties (serialize then deserialize = identity)
- Idempotence (doing twice = doing once)

---

## Async Tests

For I/O operations, use `testCaseAsync`.

```fsharp
[<Tests>]
let asyncTests =
    testList "Persistence" [
        testCaseAsync "getAllItems returns list" <| async {
            let! items = Persistence.getAllItems()
            Expect.isNotNull items "Should return list"
        }

        testCaseAsync "getById returns None for nonexistent" <| async {
            let! result = Persistence.getById 999999
            Expect.isNone result "Should not find item"
        }

        testCaseAsync "insert then get returns same item" <| async {
            let item = { Id = 0; Title = "Test" }
            let! inserted = Persistence.insert item
            let! retrieved = Persistence.getById inserted.Id
            Expect.isSome retrieved "Should find item"
            Expect.equal retrieved.Value.Title item.Title "Should have same title"
        }
    ]
```

---

## Anti-Patterns to Avoid

❌ **Testing Implementation, Not Behavior**
```fsharp
// BAD: Testing internal structure
testCase "uses List.map internally" <| fun () ->
    // Don't test HOW it works, test WHAT it produces
```
*Why bad*: Breaks when you refactor, doesn't verify behavior.
*Better*: Test inputs and outputs.

❌ **Ignoring Edge Cases**
```fsharp
// BAD: Only testing happy path
testCase "validates email" <| fun () ->
    let result = validate "user@example.com"
    Expect.isOk result
// What about empty? Whitespace? Missing @? Too long?
```
*Why bad*: Bugs hide in edge cases.
*Better*: Test boundaries, empty, null, max length, invalid formats.

❌ **Slow Tests in Main Suite**
```fsharp
// BAD: Database test mixed with unit tests
testCase "saves to database" <| fun () ->
    // Slow I/O operation
```
*Why bad*: Slows down feedback loop.
*Better*: Separate integration tests, run them separately.

❌ **Non-Deterministic Tests**
```fsharp
// BAD: Depends on current time
testCase "item is recent" <| fun () ->
    let item = Domain.create request
    Expect.isLessThan (DateTime.Now - item.CreatedAt).TotalSeconds 1.0
```
*Why bad*: Flaky tests erode trust.
*Better*: Inject time or test relative properties.

---

## Variation Guidance

**Adapt test depth to risk:**

- **Critical business logic**: Thorough tests, property-based tests, edge cases
- **Simple CRUD**: Basic happy path, key error cases
- **UI rendering**: Snapshot tests if valuable, otherwise trust the types
- **Integration**: Fewer tests, focused on contracts

The goal is confidence, not coverage percentage.

---

## Running Tests

```bash
# Run all tests
dotnet test

# Run with watch (re-runs on changes)
dotnet watch test

# Run specific tests
dotnet test --filter "FullyQualifiedName~Domain"

# Verbose output
dotnet test --logger "console;verbosity=detailed"
```

---

## Remember

Tests are an investment. Focus that investment where it matters: pure domain logic, validation rules, state transitions. These are the places where bugs have real consequences and where tests are easy to write.

**The goal**: When a test fails, you should immediately know what broke and why—without debugging.

## Related Documentation

- `/docs/06-TESTING.md` - Detailed testing guide

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/heimeshoff) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

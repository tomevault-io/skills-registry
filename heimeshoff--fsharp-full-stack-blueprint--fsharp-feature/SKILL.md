---
name: fsharp-feature
description: | Use when this capability is needed.
metadata:
  author: heimeshoff
---

# F# Full-Stack Feature Development

## Philosophy: Types First, Then Flow

A feature is a vertical slice through the entire stack. The shared types define the contract, and everything else flows from there.

**Before implementing, ask:**
- What data does this feature need? (Types first)
- What operations does the user want to perform? (API contract)
- What state transitions exist? (Domain logic)
- How should the UI reflect these states? (Frontend state)

**Core Principles:**

1. **Types Are the Specification**: If you can't express it in types, you don't understand it yet. Define types before writing any logic.

2. **Contract-Driven Development**: The API interface in `Shared/Api.fs` is a contract between frontend and backend. Define it early, implement against it.

3. **Vertical Not Horizontal**: Implement one feature completely rather than all features partially. Each feature should be deployable.

4. **Errors Are First-Class**: Design error states alongside success states. They're not afterthoughts—they're part of the feature.

---

## Development Order

```
1. Shared Types     → What data exists
2. API Contract     → What operations are possible
3. Validation       → What makes input invalid
4. Domain Logic     → How data transforms (PURE)
5. Persistence      → How data is stored
6. API Impl         → How layers connect
7. Frontend State   → How UI tracks state
8. Frontend View    → How state renders
9. Tests            → How we verify it works
```

**Why this order?** Each layer depends only on layers above it. Types inform everything. API contract shapes both frontend and backend. This eliminates rework.

---

## Step 1: Define Shared Types (`src/Shared/Domain.fs`)

**The mental model**: Think of types as the vocabulary of your feature. Until you have words for concepts, you can't discuss them.

```fsharp
module Shared.Domain

open System

// Core entity
type Task = {
    Id: int
    Title: string
    Description: string option
    Status: TaskStatus
    Priority: Priority
    DueDate: DateTime option
    CreatedAt: DateTime
}

// State machine as union
and TaskStatus =
    | Pending
    | InProgress
    | Completed
    | Cancelled

// Constrained values
and Priority = Low | Medium | High | Urgent

// Request/response shapes
type CreateTaskRequest = {
    Title: string
    Description: string option
    Priority: Priority
    DueDate: DateTime option
}

type UpdateTaskRequest = {
    Title: string option
    Description: string option
    Priority: Priority option
    Status: TaskStatus option
}
```

**Key decisions:**
- Records for data, unions for states
- `option` for truly optional fields
- Separate request types from entities

---

## Step 2: Define API Contract (`src/Shared/Api.fs`)

**The mental model**: This is the bridge between frontend and backend. Both sides code against this contract.

```fsharp
module Shared.Api

open Domain

type ITaskApi = {
    // Queries - always return data (empty list if none)
    getAll: unit -> Async<Task list>
    getByStatus: TaskStatus -> Async<Task list>

    // Commands - may fail, use Result
    getById: int -> Async<Result<Task, string>>
    create: CreateTaskRequest -> Async<Result<Task, string>>
    update: int * UpdateTaskRequest -> Async<Result<Task, string>>
    delete: int -> Async<Result<unit, string>>

    // State transitions
    start: int -> Async<Result<Task, string>>
    complete: int -> Async<Result<Task, string>>
    cancel: int -> Async<Result<Task, string>>
}
```

**Return type guide:**
- `Async<'T list>` → Query that returns collection
- `Async<Result<'T, string>>` → Operation that may fail
- `Async<unit>` → Fire-and-forget (rare, prefer Result)

---

## Step 3-6: Implement Backend

Use the **fsharp-backend** skill for detailed patterns. Summary:

### Validation (`src/Server/Validation.fs`)
```fsharp
let validateCreate (req: CreateTaskRequest) =
    let errors = [
        if String.IsNullOrWhiteSpace(req.Title) then Some "Title required"
        if req.Title.Length > 200 then Some "Title too long"
        match req.DueDate with
        | Some d when d < DateTime.UtcNow -> Some "Due date must be future"
        | _ -> None
    ] |> List.choose id
    if errors.IsEmpty then Ok req else Error errors
```

### Domain (`src/Server/Domain.fs`) - PURE
```fsharp
let createTask (req: CreateTaskRequest) : Task =
    {
        Id = 0
        Title = req.Title.Trim()
        Description = req.Description |> Option.map (fun d -> d.Trim())
        Status = Pending
        Priority = req.Priority
        DueDate = req.DueDate
        CreatedAt = DateTime.UtcNow
    }

// State transitions are domain logic
let startTask (task: Task) : Result<Task, string> =
    match task.Status with
    | Pending -> Ok { task with Status = InProgress }
    | _ -> Error "Can only start pending tasks"

let completeTask (task: Task) : Result<Task, string> =
    match task.Status with
    | Pending | InProgress -> Ok { task with Status = Completed }
    | _ -> Error "Cannot complete already finished task"
```

### Persistence (`src/Server/Persistence.fs`)
```fsharp
let insert (task: Task) : Async<Task> =
    async {
        use conn = getConnection()
        let! id = conn.ExecuteScalarAsync<int64>(
            """INSERT INTO Tasks (Title, Description, Status, Priority, DueDate, CreatedAt)
               VALUES (@Title, @Description, @Status, @Priority, @DueDate, @CreatedAt)
               RETURNING Id""",
            task
        ) |> Async.AwaitTask
        return { task with Id = int id }
    }
```

### API (`src/Server/Api.fs`)
```fsharp
let taskApi : ITaskApi = {
    create = fun request -> async {
        match Validation.validateCreate request with
        | Error errs -> return Error (String.concat "; " errs)
        | Ok valid ->
            let task = Domain.createTask valid
            let! saved = Persistence.insert task
            return Ok saved
    }

    start = fun id -> async {
        match! Persistence.getById id with
        | None -> return Error "Task not found"
        | Some task ->
            match Domain.startTask task with
            | Error e -> return Error e
            | Ok started ->
                do! Persistence.update started
                return Ok started
    }
    // ... other endpoints
}
```

---

## Step 7: Frontend State (`src/Client/State.fs`)

**The mental model**: Frontend state is a pure state machine. Messages are events, update is the transition function.

```fsharp
module State

open Elmish
open Shared.Domain
open Types

type Model = {
    // Loaded data
    Tasks: RemoteData<Task list>
    SelectedTask: RemoteData<Task>

    // Form state
    NewTask: CreateTaskRequest
    IsCreating: bool

    // UI state
    Filter: TaskStatus option
    ErrorMessage: string option
}

type Msg =
    // Load operations
    | LoadTasks
    | TasksLoaded of Result<Task list, string>
    | LoadTask of int
    | TaskLoaded of Result<Task, string>

    // Create operation
    | UpdateNewTitle of string
    | UpdateNewDescription of string
    | UpdateNewPriority of Priority
    | SubmitNewTask
    | TaskCreated of Result<Task, string>

    // State transitions
    | StartTask of int
    | CompleteTask of int
    | TaskUpdated of Result<Task, string>

    // UI
    | SetFilter of TaskStatus option
    | ClearError
```

### Update Function Pattern

```fsharp
let update (msg: Msg) (model: Model) : Model * Cmd<Msg> =
    match msg with
    // Load tasks
    | LoadTasks ->
        { model with Tasks = Loading },
        Cmd.OfAsync.either
            Api.taskApi.getAll ()
            (Ok >> TasksLoaded)
            (fun ex -> Error ex.Message |> TasksLoaded)

    | TasksLoaded (Ok tasks) ->
        { model with Tasks = Success tasks }, Cmd.none

    | TasksLoaded (Error err) ->
        { model with Tasks = Failure err; ErrorMessage = Some err }, Cmd.none

    // Submit new task
    | SubmitNewTask ->
        { model with IsCreating = true },
        Cmd.OfAsync.either
            Api.taskApi.create model.NewTask
            TaskCreated
            (fun ex -> Error ex.Message |> TaskCreated)

    | TaskCreated (Ok _) ->
        { model with
            NewTask = emptyRequest
            IsCreating = false },
        Cmd.ofMsg LoadTasks

    | TaskCreated (Error err) ->
        { model with IsCreating = false; ErrorMessage = Some err }, Cmd.none

    // State transitions
    | StartTask id ->
        model,
        Cmd.OfAsync.either
            Api.taskApi.start id
            TaskUpdated
            (fun ex -> Error ex.Message |> TaskUpdated)

    | TaskUpdated (Ok _) ->
        model, Cmd.ofMsg LoadTasks

    | TaskUpdated (Error err) ->
        { model with ErrorMessage = Some err }, Cmd.none
```

---

## Step 8: Frontend View (`src/Client/View.fs`)

**The mental model**: Views are pure functions from Model to UI. Pattern match exhaustively on RemoteData.

```fsharp
module View

open Feliz
open Shared.Domain
open State
open Types

let taskCard (task: Task) (dispatch: Msg -> unit) =
    Html.div [
        prop.className "card bg-base-100 shadow-md"
        prop.children [
            Html.div [
                prop.className "card-body"
                prop.children [
                    // Header with status badge
                    Html.div [
                        prop.className "flex justify-between items-center"
                        prop.children [
                            Html.h3 [ prop.className "card-title"; prop.text task.Title ]
                            statusBadge task.Status
                        ]
                    ]

                    // Actions based on status
                    Html.div [
                        prop.className "card-actions justify-end mt-4"
                        prop.children (
                            match task.Status with
                            | Pending ->
                                [ Html.button [
                                    prop.className "btn btn-primary btn-sm"
                                    prop.text "Start"
                                    prop.onClick (fun _ -> dispatch (StartTask task.Id))
                                  ] ]
                            | InProgress ->
                                [ Html.button [
                                    prop.className "btn btn-success btn-sm"
                                    prop.text "Complete"
                                    prop.onClick (fun _ -> dispatch (CompleteTask task.Id))
                                  ] ]
                            | Completed | Cancelled -> []
                        )
                    ]
                ]
            ]
        ]
    ]

let taskList (model: Model) (dispatch: Msg -> unit) =
    match model.Tasks with
    | NotAsked ->
        Html.div [
            prop.className "text-center p-8"
            prop.children [
                Html.button [
                    prop.className "btn btn-primary"
                    prop.text "Load Tasks"
                    prop.onClick (fun _ -> dispatch LoadTasks)
                ]
            ]
        ]

    | Loading ->
        Html.div [
            prop.className "flex justify-center p-8"
            prop.children [ Html.span [ prop.className "loading loading-spinner loading-lg" ] ]
        ]

    | Success tasks when List.isEmpty tasks ->
        Html.div [
            prop.className "alert alert-info"
            prop.text "No tasks yet. Create one to get started!"
        ]

    | Success tasks ->
        Html.div [
            prop.className "grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4"
            prop.children [ for task in tasks -> taskCard task dispatch ]
        ]

    | Failure error ->
        Html.div [
            prop.className "alert alert-error"
            prop.children [
                Html.span [ prop.text error ]
                Html.button [
                    prop.className "btn btn-sm btn-ghost"
                    prop.text "Retry"
                    prop.onClick (fun _ -> dispatch LoadTasks)
                ]
            ]
        ]
```

---

## Step 9: Tests

**The mental model**: Test the pure parts (domain, validation, update). Don't mock what you don't have to.

```fsharp
module TaskTests

open Expecto
open Shared.Domain

[<Tests>]
let domainTests =
    testList "Task Domain" [
        testCase "createTask sets pending status" <| fun () ->
            let request = { Title = "Test"; Description = None; Priority = Medium; DueDate = None }
            let task = Domain.createTask request
            Expect.equal task.Status Pending "Should be pending"

        testCase "startTask only works for pending" <| fun () ->
            let pending = { baseTask with Status = Pending }
            let inProgress = { baseTask with Status = InProgress }

            Expect.isOk (Domain.startTask pending) "Should start pending"
            Expect.isError (Domain.startTask inProgress) "Cannot restart"

        testCase "completeTask works for pending and in-progress" <| fun () ->
            let pending = { baseTask with Status = Pending }
            let inProgress = { baseTask with Status = InProgress }
            let completed = { baseTask with Status = Completed }

            Expect.isOk (Domain.completeTask pending) "Can complete pending"
            Expect.isOk (Domain.completeTask inProgress) "Can complete in-progress"
            Expect.isError (Domain.completeTask completed) "Cannot re-complete"
    ]

[<Tests>]
let validationTests =
    testList "Task Validation" [
        testCase "empty title fails" <| fun () ->
            let req = { Title = ""; Description = None; Priority = Low; DueDate = None }
            Expect.isError (Validation.validateCreate req) "Should reject empty title"

        testCase "past due date fails" <| fun () ->
            let yesterday = DateTime.UtcNow.AddDays(-1.0)
            let req = { Title = "Test"; Description = None; Priority = Low; DueDate = Some yesterday }
            Expect.isError (Validation.validateCreate req) "Should reject past date"
    ]
```

---

## Anti-Patterns to Avoid

❌ **Starting with UI**
*Why bad*: You don't know what data you need yet.
*Better*: Types first, then API, then UI.

❌ **Horizontal slices**
*Why bad*: "All the types, then all the backend, then all the frontend" leaves nothing deployable.
*Better*: One complete feature at a time.

❌ **Ignoring error states**
*Why bad*: Loading and error states are part of the feature, not polish.
*Better*: Design `RemoteData` states from the start.

❌ **Copy-paste types**
*Why bad*: Drift between frontend and backend types.
*Better*: Single source of truth in Shared project.

---

## Variation Guidance

**Adapt the depth of each layer to your feature:**

- **Simple CRUD feature**: Minimal domain logic, straightforward validation
- **Complex business feature**: Rich domain logic, state machine transitions
- **Read-heavy feature**: Focus on queries, caching considerations
- **Write-heavy feature**: Focus on validation, optimistic updates

The process is constant; the emphasis varies with the feature's nature.

---

## Remember

A well-implemented feature is a complete vertical slice—types to UI to tests. When you're done, the feature works end-to-end and can be shipped independently.

**The goal**: After implementing a feature, you should be able to demo it, test it, and deploy it—without touching unrelated code.

## Related Skills

- **fsharp-shared** - Detailed type patterns
- **fsharp-backend** - Backend layer implementation
- **fsharp-frontend** - Frontend patterns
- **fsharp-tests** - Testing strategies

## Related Documentation

- `/docs/09-QUICK-REFERENCE.md` - Code templates
- `CLAUDE.md` - Project conventions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/heimeshoff) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

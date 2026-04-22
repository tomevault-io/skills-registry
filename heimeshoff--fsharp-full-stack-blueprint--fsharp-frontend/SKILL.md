---
name: fsharp-frontend
description: | Use when this capability is needed.
metadata:
  author: heimeshoff
---

# F# Frontend Implementation (Elmish + Feliz)

## Philosophy: State Machines, Not Spaghetti

The Elmish MVU pattern treats your entire UI as a state machine. The Model is the state, Messages are transitions, and Views are renderings of that state.

**Before implementing, ask:**
- What states can this UI be in? (Model)
- What events can occur? (Messages)
- How do events change state? (Update)
- How does each state look? (View)

**Core Principles:**

1. **State Is Explicit**: If the UI can show it, the Model must contain it. No hidden state in closures or refs.

2. **Effects Are Declarative**: Side effects (API calls, timers) are commands returned from update, not imperative calls.

3. **Views Are Pure Functions**: `Model -> ReactElement`. Given the same model, always render the same UI.

4. **Async States Are First-Class**: Use `RemoteData<'T>` for any data that comes from an async source. Loading and error states are not afterthoughts.

---

## The MVU Loop

```
┌─────────────────────────────────────┐
│                                     │
▼                                     │
View ──(user action)──> Msg           │
                         │            │
                         ▼            │
                     Update           │
                         │            │
              ┌──────────┴──────────┐ │
              ▼                     ▼ │
           Model                  Cmd ─┘
              │                (async result)
              ▼
           View (re-render)
```

**Mental model**: The user interacts with the View, which dispatches Messages. Update processes messages and returns new Model + Commands. Commands may dispatch more messages when complete.

---

## RemoteData: The Async State Pattern

**Never represent async data as just the data type.** An API response isn't `Task list`—it's one of four states:

```fsharp
type RemoteData<'T> =
    | NotAsked    // Haven't requested yet
    | Loading     // Request in progress
    | Success of 'T
    | Failure of string
```

**Why this matters**: Without RemoteData, you end up with boolean flags (`isLoading`, `hasError`) that can represent impossible states. RemoteData makes illegal states unrepresentable.

---

## State Management (`src/Client/State.fs`)

### Model: The Complete UI State

```fsharp
type Model = {
    // Server data (always RemoteData for async)
    Items: RemoteData<Item list>
    SelectedItem: RemoteData<Item>

    // Form state
    FormTitle: string
    FormDescription: string
    IsSubmitting: bool

    // UI state
    IsModalOpen: bool
    ActiveTab: Tab
    SearchQuery: string
}
```

**Key insight**: If you can see it or interact with it in the UI, it should be in the Model.

### Messages: All Possible Events

```fsharp
type Msg =
    // Async operations follow Request/Response pattern
    | LoadItems
    | ItemsLoaded of Result<Item list, string>

    | LoadItem of int
    | ItemLoaded of Result<Item, string>

    | SubmitForm
    | FormSubmitted of Result<Item, string>

    // Form updates
    | UpdateTitle of string
    | UpdateDescription of string

    // UI interactions
    | OpenModal
    | CloseModal
    | SwitchTab of Tab
    | UpdateSearch of string
```

**Pattern**: For each async operation, define a "trigger" message and a "result" message. The result message carries a `Result<'T, string>` to handle both success and failure.

### Init: Starting State

```fsharp
let init () : Model * Cmd<Msg> =
    let model = {
        Items = NotAsked
        SelectedItem = NotAsked
        FormTitle = ""
        FormDescription = ""
        IsSubmitting = false
        IsModalOpen = false
        ActiveTab = Overview
        SearchQuery = ""
    }
    // Optionally start loading data immediately
    model, Cmd.ofMsg LoadItems
```

### Update: State Transitions

```fsharp
let update (msg: Msg) (model: Model) : Model * Cmd<Msg> =
    match msg with
    // Async: Request phase
    | LoadItems ->
        { model with Items = Loading },
        Cmd.OfAsync.either
            Api.itemApi.getAll ()
            (Ok >> ItemsLoaded)
            (fun ex -> Error ex.Message |> ItemsLoaded)

    // Async: Success
    | ItemsLoaded (Ok items) ->
        { model with Items = Success items }, Cmd.none

    // Async: Failure
    | ItemsLoaded (Error err) ->
        { model with Items = Failure err }, Cmd.none

    // Form submission
    | SubmitForm ->
        let request = { Title = model.FormTitle; Description = model.FormDescription }
        { model with IsSubmitting = true },
        Cmd.OfAsync.either
            Api.itemApi.create request
            FormSubmitted
            (fun ex -> Error ex.Message |> FormSubmitted)

    | FormSubmitted (Ok item) ->
        { model with
            IsSubmitting = false
            FormTitle = ""
            FormDescription = ""
            IsModalOpen = false },
        Cmd.ofMsg LoadItems  // Reload list after creation

    | FormSubmitted (Error err) ->
        { model with IsSubmitting = false },
        Cmd.none  // Could add error to model

    // Synchronous state updates
    | UpdateTitle title ->
        { model with FormTitle = title }, Cmd.none

    | OpenModal ->
        { model with IsModalOpen = true }, Cmd.none

    | CloseModal ->
        { model with IsModalOpen = false }, Cmd.none
```

---

## View Components (`src/Client/View.fs`)

### The RemoteData View Pattern

**Always handle all four states:**

```fsharp
let itemsView (data: RemoteData<Item list>) (dispatch: Msg -> unit) =
    match data with
    | NotAsked ->
        Html.div [
            prop.className "text-center p-8 text-gray-500"
            prop.text "Click to load items"
        ]

    | Loading ->
        Html.div [
            prop.className "flex justify-center p-8"
            prop.children [
                Html.span [ prop.className "loading loading-spinner loading-lg" ]
            ]
        ]

    | Success items when List.isEmpty items ->
        Html.div [
            prop.className "alert alert-info"
            prop.text "No items yet. Create one to get started!"
        ]

    | Success items ->
        Html.div [
            prop.className "grid grid-cols-1 md:grid-cols-2 gap-4"
            prop.children [ for item in items -> itemCard item dispatch ]
        ]

    | Failure error ->
        Html.div [
            prop.className "alert alert-error"
            prop.children [
                Html.span [ prop.text error ]
                Html.button [
                    prop.className "btn btn-sm btn-ghost"
                    prop.text "Retry"
                    prop.onClick (fun _ -> dispatch LoadItems)
                ]
            ]
        ]
```

**Why exhaustive matching?** The compiler ensures you handle every state. No forgotten loading spinners or error messages.

### Component Patterns

**Card Component:**
```fsharp
let itemCard (item: Item) (dispatch: Msg -> unit) =
    Html.div [
        prop.className "card bg-base-100 shadow-md hover:shadow-lg transition-shadow"
        prop.children [
            Html.div [
                prop.className "card-body"
                prop.children [
                    Html.h2 [
                        prop.className "card-title"
                        prop.text item.Title
                    ]
                    Html.p [
                        prop.className "text-gray-600"
                        prop.text item.Description
                    ]
                    Html.div [
                        prop.className "card-actions justify-end"
                        prop.children [
                            Html.button [
                                prop.className "btn btn-primary btn-sm"
                                prop.text "View"
                                prop.onClick (fun _ -> dispatch (LoadItem item.Id))
                            ]
                        ]
                    ]
                ]
            ]
        ]
    ]
```

**Form Component:**
```fsharp
let createForm (model: Model) (dispatch: Msg -> unit) =
    Html.form [
        prop.className "space-y-4"
        prop.onSubmit (fun e ->
            e.preventDefault()
            dispatch SubmitForm)
        prop.children [
            // Title input
            Html.div [
                prop.className "form-control"
                prop.children [
                    Html.label [
                        prop.className "label"
                        prop.children [ Html.span [ prop.className "label-text"; prop.text "Title" ] ]
                    ]
                    Html.input [
                        prop.className "input input-bordered w-full"
                        prop.type' "text"
                        prop.value model.FormTitle
                        prop.onChange (UpdateTitle >> dispatch)
                        prop.disabled model.IsSubmitting
                    ]
                ]
            ]

            // Description textarea
            Html.div [
                prop.className "form-control"
                prop.children [
                    Html.label [
                        prop.className "label"
                        prop.children [ Html.span [ prop.className "label-text"; prop.text "Description" ] ]
                    ]
                    Html.textarea [
                        prop.className "textarea textarea-bordered w-full"
                        prop.value model.FormDescription
                        prop.onChange (UpdateDescription >> dispatch)
                        prop.disabled model.IsSubmitting
                    ]
                ]
            ]

            // Submit button
            Html.button [
                prop.className "btn btn-primary w-full"
                prop.type' "submit"
                prop.disabled (model.IsSubmitting || String.IsNullOrWhiteSpace model.FormTitle)
                prop.children [
                    if model.IsSubmitting then
                        Html.span [ prop.className "loading loading-spinner loading-sm" ]
                    else
                        Html.text "Create"
                ]
            ]
        ]
    ]
```

### Conditional Rendering

```fsharp
// If/else for simple cases
if model.IsModalOpen then
    modalComponent model dispatch
else
    Html.none

// Pattern matching for multiple states
match model.ActiveTab with
| Overview -> overviewTab model dispatch
| Details -> detailsTab model dispatch
| Settings -> settingsTab model dispatch
```

---

## Anti-Patterns to Avoid

❌ **Imperative API Calls in Views**
```fsharp
// BAD: Calling API directly in view
Html.button [
    prop.onClick (fun _ ->
        async {
            let! result = Api.saveItem item
            // Now what? Can't update model!
        } |> Async.StartImmediate)
]
```
*Why bad*: No way to update model, no error handling.
*Better*: Dispatch a message, handle async in update with Cmd.

❌ **Boolean Flags for Loading State**
```fsharp
// BAD: Impossible states possible
type Model = {
    Items: Item list
    IsLoading: bool
    HasError: bool
    ErrorMessage: string
}
// Can be: IsLoading=true AND HasError=true AND Items=[...] ???
```
*Why bad*: Multiple flags can represent impossible states.
*Better*: Use `RemoteData<Item list>`.

❌ **Hidden State Outside Model**
```fsharp
// BAD: Using mutable ref
let mutable lastClickTime = DateTime.MinValue

let view model dispatch =
    Html.button [
        prop.onClick (fun _ ->
            lastClickTime <- DateTime.Now  // Hidden state!
            dispatch Click)
    ]
```
*Why bad*: State lives outside the MVU loop, breaks time-travel debugging.
*Better*: Put `lastClickTime` in Model.

❌ **Not Handling All RemoteData Cases**
```fsharp
// BAD: Ignoring states
match model.Data with
| Success items -> renderItems items
| _ -> Html.none  // Silent failure!
```
*Why bad*: User sees nothing during loading or error.
*Better*: Handle all four cases explicitly.

---

## Variation Guidance

**Adapt the pattern to your UI complexity:**

- **Simple form**: Model has form fields, few messages
- **Data-heavy dashboard**: Multiple RemoteData fields, loading orchestration
- **Real-time updates**: WebSocket messages integrated into update
- **Wizard/multi-step**: Model tracks step, each step has sub-model

The MVU structure is constant; the Model and Message complexity varies.

**Styling variation:**
- Cards, grids, and lists for data display
- Modals and drawers for focused interactions
- Tabs and accordions for content organization
- Choose based on information density and user workflow

---

## TailwindCSS + DaisyUI Quick Reference

**Layout:**
- `container mx-auto p-4` - Centered container
- `flex flex-col gap-4` - Vertical stack
- `grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4` - Responsive grid

**DaisyUI Components:**
- `btn btn-primary btn-sm` - Button
- `card bg-base-100 shadow-xl` - Card
- `input input-bordered` - Input
- `alert alert-error` - Alert
- `loading loading-spinner` - Loading spinner
- `badge badge-primary` - Badge

**Utility:**
- `text-center`, `text-left` - Text alignment
- `p-4`, `m-2`, `gap-4` - Spacing
- `rounded-lg`, `shadow-md` - Shapes
- `hover:shadow-lg`, `transition-shadow` - Interactions

---

## Remember

The MVU pattern seems restrictive at first, but it provides guarantees: predictable state, testable logic, and explicit effects. Every piece of UI behavior is traceable through messages.

**The goal**: Anyone reading your frontend code should be able to trace any UI behavior from the message that triggered it through the model change to the view that renders it.

## Related Documentation

- `/docs/02-FRONTEND-GUIDE.md` - Detailed frontend patterns
- `/docs/09-QUICK-REFERENCE.md` - Code templates

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/heimeshoff) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

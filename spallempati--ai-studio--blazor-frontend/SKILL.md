---
name: blazor-frontend
description: | Use when this capability is needed.
metadata:
  author: spallempati
---

# Blazor Frontend Development Standards

## Core Requirements

- **MUST** use dual-file component structure (`.razor` + `.razor.cs` code-behind)
- **MUST NOT** use `@code` blocks in `.razor` files
- **MUST** use `public partial class ComponentName : ComponentBase`
- **MUST** follow PascalCase for component and method names
- **MUST** use `[Inject]` attribute for dependency injection

## File Organization

### .razor File Contains

- HTML markup and Razor syntax (`@if`, `@foreach`, etc.)
- Component references and structure
- CSS classes and styling references
- Razor comments (`@* ... *@`) for component summaries

### .razor.cs File Contains

- `public partial class ComponentName : ComponentBase`
- All `[Inject]` services
- All `[Parameter]` properties
- Private fields, properties, and ViewModels
- Lifecycle methods and event handlers
- All C# logic and business rules

## Naming Conventions

| Element | Convention | Example |
|---------|------------|---------|
| Components | `PascalCase` | `UserProfile`, `DataGrid` |
| Event handlers | `On{Action}{Context}` | `OnSaveClicked`, `OnFilterChanged` |
| Field changed | `On{Context}FieldChanged` | `OnBasicInfoFieldChanged` |
| Dropdown changed | `On{FieldName}Changed` | `OnStateSelectionChanged` |
| Data loading | `Load{DataType}Async` | `LoadUserDetailsAsync` |
| Parameters | Descriptive names | `UserId`, `IsEnabled` |
| Private fields | `camelCase` | `_userService`, `_isLoading` |

## Code-Behind Organization

- **MUST** organize members in this order:
  1. Dependency Injection (`[Inject]`)
  2. Parameters (`[Parameter]`)
  3. ViewModels and private fields
  4. Lifecycle methods
  5. Event handlers
  6. Private helper methods

- **SHOULD** use comment headers with `═══` separators (not C# regions)

```csharp
public partial class UserProfile : ComponentBase
{
    // ════════════════════════════════════════════════════════════════
    // Dependency Injection
    // ════════════════════════════════════════════════════════════════
    [Inject] private IUserService UserService { get; set; } = default!;
    [Inject] private ILogger<UserProfile> Logger { get; set; } = default!;

    // ════════════════════════════════════════════════════════════════
    // Parameters
    // ════════════════════════════════════════════════════════════════
    [Parameter] public int UserId { get; set; }
    [Parameter] public EventCallback OnSaved { get; set; }

    // ════════════════════════════════════════════════════════════════
    // ViewModel
    // ════════════════════════════════════════════════════════════════
    public UserViewModel UserVM { get; set; } = new();

    // ════════════════════════════════════════════════════════════════
    // Lifecycle Methods
    // ════════════════════════════════════════════════════════════════
    protected override async Task OnInitializedAsync()
    {
        await LoadUserAsync();
    }

    // ════════════════════════════════════════════════════════════════
    // Event Handlers
    // ════════════════════════════════════════════════════════════════
    private async Task OnSaveClicked()
    {
        // Handle save
    }
}
```

## Lifecycle Methods

- **SHOULD** use async versions when performing I/O operations
- **MUST** call `StateHasChanged()` only when necessary
- **MUST NOT** perform heavy operations in `OnInitialized` (use `OnInitializedAsync`)

| Method | Use Case |
|--------|----------|
| `OnInitialized` / `OnInitializedAsync` | Initial data loading |
| `OnParametersSet` / `OnParametersSetAsync` | React to parameter changes |
| `OnAfterRender` / `OnAfterRenderAsync` | DOM interactions, JS interop |

## Event Handling

- **MUST** use `async Task` for async event handlers (not `async void`)
- **MUST** use `EventCallback<T>` for component communication
- **MUST** always use `await` (never `.Result` or `.Wait()`)
- **MUST** handle exceptions with try-catch blocks
- **SHOULD** use `changeEventArgs` (not `e`) for ChangeEventArgs parameter

```csharp
// Button click handler with validation
private async Task OnSaveClicked()
{
    try
    {
        if (!ValidateForm())
        {
            ShowWarningToast("Please correct validation errors");
            return;
        }
        await UserService.SaveAsync(UserVM.CurrentUser);
        await OnSaved.InvokeAsync();
    }
    catch (Exception ex)
    {
        Logger.LogError(ex, "Error saving user");
        ShowErrorToast("An unexpected error occurred");
    }
}

// Dropdown selection handler
private async Task OnStatusChanged(ChangeEventArgs changeEventArgs)
{
    if (int.TryParse(changeEventArgs.Value?.ToString(), out int statusId))
    {
        UserVM.Status = statusId == 0 ? null : statusId;
        await OnFieldChanged.InvokeAsync();
    }
}
```

## Dependency Injection

- **MUST** use `[Inject]` attribute (never constructor injection in components)
- **MUST** place injected services at the top of the class
- **MUST** use `default!` for non-nullable reference types
- **SHOULD** use private properties with PascalCase names

```csharp
[Inject] private IUserService UserService { get; set; } = default!;
[Inject] private ILogger<ComponentName> Logger { get; set; } = default!;
[Inject] private NavigationManager Navigation { get; set; } = default!;
```

## Data Binding

### One-Way Binding

```razor
<input value="@Value" />
<span>@DisplayText</span>
<div class="btn @(IsActive ? "btn-primary" : "btn-secondary")">Button</div>
```

### Two-Way Binding

```razor
<input @bind="Value" />
<input @bind="Value" @bind:event="oninput" />
<input @bind="DateValue" @bind:format="yyyy-MM-dd" />
```

## Conditional and List Rendering

```razor
@if (IsVisible)
{
    <div>Visible content</div>
}

@foreach (var item in Items)
{
    <li @key="item.Id">@item.Name</li>
}

@foreach (var (item, index) in Items.Select((item, i) => (item, i)))
{
    <tr class="@(index % 2 == 0 ? "table-light" : "")">
        <td>@item.Name</td>
    </tr>
}
```

## Component Parameters and Slots

```csharp
// In .razor.cs
[Parameter] public string Title { get; set; } = string.Empty;
[Parameter] public RenderFragment? Body { get; set; }
[Parameter] public RenderFragment? Footer { get; set; }
[Parameter] public EventCallback OnClose { get; set; }
[Parameter] public EventCallback<string> OnSelect { get; set; }
```

## Anti-Patterns

- **MUST NOT** use inline JavaScript (use `@onclick` and C# methods)
- **MUST NOT** manipulate DOM directly (use Blazor state and re-rendering)
- **MUST NOT** use jQuery (use Blazor's built-in features)
- **MUST NOT** use `document.getElementById` (use `@ref` for element references)
- **MUST NOT** use `.Result` or `.Wait()` on async operations
- **MUST NOT** use empty catch blocks
- **MUST NOT** use generic parameter names like `Model`, `Data`, `Item`
- **MUST NOT** use generic method names like `HandleFieldChange`, `OnChange`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spallempati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

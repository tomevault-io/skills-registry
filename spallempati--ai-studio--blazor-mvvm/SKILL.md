---
name: blazor-mvvm
description: | Use when this capability is needed.
metadata:
  author: spallempati
---

# Blazor MVVM Patterns

## Core Principles

- **MUST** keep UI state in ViewModel, not in code-behind
- **MUST** use flat ViewModel structure (no nested state classes)
- **MUST** move business logic to ViewModel for testability
- **MUST NOT** expose DTOs directly as ViewModel properties
- **MUST** keep code-behind under 300 lines

## The 9 MVVM Rules

| Rule | Name | Description |
|:---:|:---|:---|
| 1 | UI State in VM | All UI state **MUST** be in ViewModel |
| 2 | Flat VM | No nested state classes |
| 3 | No DTOs in VM | ViewModel works with UI Models ONLY |
| 4 | No DTO Binding | Razor binds to UI Models only |
| 5 | Thin Code-Behind | Code-behind < 300 lines |
| 6 | Standard Naming | `{Feature}VM`, `On{Action}{Context}` methods |
| 7 | VM Grouping | Visual separators `═══` with headers |
| 8 | UI Service | UI Service handles DTO↔Model mapping |
| 9 | Business Logic in VM | Testable methods **MUST** be in ViewModel |

## Data Flow Architecture

```
Infrastructure (DTOs) → UI Service (DTO↔Model mapping) → ViewModel (UI Models ONLY) → Razor
```

- **MUST** keep DTOs in UI Service layer only
- **MUST** have UI Service handle all DTO↔Model mapping
- **MUST** have ViewModel work with UI Models only
- **MUST NOT** put DTOs in ViewModel (even as private fields)
- **MUST NOT** have ViewModel know about DTOs

## ViewModel Structure

- **MUST** name ViewModels as `{Feature}ViewModel` or `{Feature}VM`
- **MUST** use properties for all UI state
- **MUST** initialize collections to empty (not null)
- **SHOULD** use `INotifyPropertyChanged` when needed
- **MUST NOT** include HTTP calls directly (use UI Service)

```csharp
public class UserProfileViewModel
{
    // ═══════════════════════════════════════════════════════════════════════════
    // UI State
    // ═══════════════════════════════════════════════════════════════════════════
    public bool IsLoading { get; set; }
    public bool IsEditing { get; set; }
    public string SearchTerm { get; set; } = string.Empty;
    public string? ErrorMessage { get; set; }

    // ═══════════════════════════════════════════════════════════════════════════
    // Data Properties (UI Models only, no DTOs)
    // ═══════════════════════════════════════════════════════════════════════════
    public UserModel CurrentUser { get; set; } = new();
    public List<RoleModel> AvailableRoles { get; set; } = new();

    // ═══════════════════════════════════════════════════════════════════════════
    // Computed Properties
    // ═══════════════════════════════════════════════════════════════════════════
    public bool HasData => CurrentUser.Id > 0;
    public bool CanSave => !IsLoading && IsValid;
    public bool IsValid => !string.IsNullOrEmpty(CurrentUser.Name);

    // ═══════════════════════════════════════════════════════════════════════════
    // Testable Methods (Business Logic)
    // ═══════════════════════════════════════════════════════════════════════════
    public List<RoleModel> GetFilteredRoles()
    {
        if (string.IsNullOrEmpty(SearchTerm))
            return AvailableRoles;

        return AvailableRoles
            .Where(r => r.Name.Contains(SearchTerm, StringComparison.OrdinalIgnoreCase))
            .ToList();
    }

    public bool ValidateUser()
    {
        if (string.IsNullOrWhiteSpace(CurrentUser.Name))
        {
            ErrorMessage = "Name is required";
            return false;
        }
        ErrorMessage = null;
        return true;
    }

    // ═══════════════════════════════════════════════════════════════════════════
    // Sync Methods
    // ═══════════════════════════════════════════════════════════════════════════
    public void SyncFromModel(UserModel model)
    {
        CurrentUser = model;
        IsEditing = false;
    }

    public void Reset()
    {
        CurrentUser = new();
        SearchTerm = string.Empty;
        ErrorMessage = null;
        IsEditing = false;
    }
}
```

## UI Service Layer

- **MUST** create `I{Feature}UIService` interface for each feature
- **MUST** handle DTO to UI Model mapping in service
- **MUST** handle API calls and error transformation
- **SHOULD** return UI-friendly error messages

```csharp
// Interface
public interface IUserUIService
{
    Task<UserModel> GetUserAsync(int id);
    Task<List<UserModel>> GetUsersAsync();
    Task<SaveResult> SaveUserAsync(UserModel user);
}

// Implementation
public class UserUIService : IUserUIService
{
    private readonly IUserDataAccess _dataAccess;
    private readonly ILogger<UserUIService> _logger;

    public UserUIService(IUserDataAccess dataAccess, ILogger<UserUIService> logger)
    {
        _dataAccess = dataAccess;
        _logger = logger;
    }

    public async Task<UserModel> GetUserAsync(int id)
    {
        var dto = await _dataAccess.GetUserAsync(id);
        return MapToModel(dto);  // DTO → UI Model mapping here
    }

    public async Task<SaveResult> SaveUserAsync(UserModel user)
    {
        try
        {
            var dto = MapToDto(user);  // UI Model → DTO mapping here
            await _dataAccess.SaveUserAsync(dto);
            return SaveResult.Success();
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error saving user {UserId}", user.Id);
            return SaveResult.Error("Failed to save user. Please try again.");
        }
    }

    private UserModel MapToModel(UserDto dto) { /* mapping */ }
    private UserDto MapToDto(UserModel model) { /* mapping */ }
}
```

## Business Logic Placement

### MUST Be in ViewModel

- Filtering and sorting logic
- Data transformation
- Validation logic
- Computed properties with logic
- Business rules
- Date/number parsing and formatting
- Any logic that should be unit tested

### Can Stay in Code-Behind

- Simple delegation: `MarkDirty()` + `EventCallback.InvokeAsync()`
- `StateHasChanged()` calls (Blazor-specific)
- Methods that ONLY invoke EventCallbacks

## Code Coverage

- **MUST** add `[ExcludeFromCodeCoverage]` to methods that will not be unit tested
  - Simple delegation methods
  - Blazor lifecycle methods
  - Event handlers that only call ViewModel

- **MUST NOT** add `[ExcludeFromCodeCoverage]` to business logic methods or ViewModel methods

```csharp
// In code-behind
[ExcludeFromCodeCoverage]  // Simple delegation, not tested
private async Task OnFieldChanged()
{
    UserVM.MarkDirty();
    await OnChanged.InvokeAsync();
}

// In ViewModel - NO ExcludeFromCodeCoverage, this MUST be tested
public bool ValidateUser()
{
    // Business logic
}
```

## When to Use ViewModel

| Scenario | Approach |
|----------|----------|
| Simple display (< 5 properties) | Direct binding to model |
| Forms with validation | ViewModel |
| Complex state management | ViewModel |
| Shared state across components | ViewModel + DI |
| List with filtering/sorting | ViewModel |
| Computed/derived values | ViewModel |

## Testing ViewModels

- **MUST** unit test ViewModel logic independently
- **MUST** mock UI services in ViewModel tests
- **SHOULD** verify state changes after method calls

```csharp
[Fact]
public void GetFilteredRoles_WithSearchTerm_ReturnsMatchingRoles()
{
    var vm = new UserProfileViewModel
    {
        AvailableRoles = new List<RoleModel>
        {
            new() { Name = "Admin" },
            new() { Name = "User" },
            new() { Name = "Administrator" }
        },
        SearchTerm = "Admin"
    };

    var result = vm.GetFilteredRoles();

    Assert.Equal(2, result.Count);
    Assert.All(result, r => Assert.Contains("Admin", r.Name));
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spallempati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

---
name: csharp-patterns
description: name: csharp-patterns Use when this capability is needed.
metadata:
  author: tridentsof
---
---
name: csharp-patterns
description: Modern C# best practices and patterns
---

# C# Patterns

> Modern C# with async/await, nullable, and clean patterns.

---

## Async/Await

```csharp
// ✅ Correct
public async Task<User> GetUserAsync(int id)
{
    return await _repository.GetByIdAsync(id);
}

// ❌ Wrong - blocking
public User GetUser(int id)
{
    return _repository.GetByIdAsync(id).Result;
}
```

---

## Nullable Reference Types

```csharp
// Enable in .csproj
<Nullable>enable</Nullable>

// Usage
public User? GetUser(int id)  // Can be null
public User GetRequiredUser(int id)  // Never null
```

---

## Records

```csharp
// Immutable data objects
public record UserDto(int Id, string Name, string Email);

// With modification
var updated = user with { Name = "New Name" };
```

---

## Pattern Matching

```csharp
var result = user switch
{
    { Role: "Admin" } => "Full access",
    { Role: "User" } => "Limited access",
    null => "No user",
    _ => "Unknown"
};
```

---

## LINQ Best Practices

```csharp
// ✅ Correct - efficient
var activeUsers = users
    .Where(u => u.IsActive)
    .Select(u => u.Name)
    .ToList();

// ❌ Wrong - N+1
foreach (var user in users)
{
    var orders = await GetOrdersAsync(user.Id);
}
```

---

## Exception Handling

```csharp
try
{
    await ProcessAsync();
}
catch (NotFoundException ex)
{
    _logger.LogWarning(ex, "Item not found: {Id}", id);
    throw;
}
catch (Exception ex)
{
    _logger.LogError(ex, "Unexpected error");
    throw;
}
```

---

## Constants & Enums

```csharp
// ✅ Correct: Use enums for types
public enum UserStatus
{
    Active,
    Inactive,
    Suspended
}

// ✅ Correct: Use constants for reusable strings
public static class AppConstants
{
    public const string DefaultLanguage = "en-US";
    public const string ApiVersion = "v1";
}
```

---

## Configuration Management

```csharp
// ✅ Correct: Use IConfiguration or environment variables
public class MyService
{
    private readonly string _apiKey;
    
    public MyService(IConfiguration config)
    {
        _apiKey = config["ApiSettings:Key"] ?? throw new ArgumentNullException("API Key is missing");
    }
}
```

---

---

## File Organization

> **CRITICAL:** One class/interface/enum per file.

```csharp
// ❌ Wrong: Multiple classes in one file
public class User { ... }
public class Order { ... }

// ✅ Correct: Separate files
// User.cs
public class User { ... }

// Order.cs
public class Order { ... }
```

```csharp
// ❌ Wrong: Nested classes
public class User 
{
    // Don't do this unless it's a private DTO used ONLY here
    public class UserConfig { ... }
}
```

---

## DO / DON'T

| ✅ Do | ❌ Don't |
|-------|---------|
| Async all the way | .Result or .Wait() |
| Nullable types | Ignore null warnings |
| Records for DTOs | Mutable classes |
| Pattern matching | Long if-else chains |
| Constants/Enums for strings | Magic strings |
| Environment variables/Config | Hardcoded settings |
| One class per file | Multiple classes/Nested |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tridentsof) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

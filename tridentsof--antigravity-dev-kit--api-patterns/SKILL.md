---
name: api-patterns
description: description: REST API design patterns and best practices Use when this capability is needed.
metadata:
  author: tridentsof
---
---
name: api-patterns
description: REST API design patterns and best practices
---

# API Patterns

> RESTful API design for ASP.NET Core.

---

## REST Conventions

| Method | Action | Route | Response |
|--------|--------|-------|----------|
| GET | List | /api/users | 200 + array |
| GET | Read | /api/users/{id} | 200 or 404 |
| POST | Create | /api/users | 201 + object |
| PUT | Update | /api/users/{id} | 200 or 404 |
| DELETE | Delete | /api/users/{id} | 204 or 404 |

---

## Response Format

```csharp
public class ApiResponse<T>
{
    public bool Success { get; set; }
    public T? Data { get; set; }
    public string? Error { get; set; }
    public List<string>? Errors { get; set; }
}
```

---

## Pagination

```csharp
[HttpGet]
public async Task<ActionResult<PagedResult<UserDto>>> GetUsers(
    [FromQuery] int page = 1,
    [FromQuery] int pageSize = 20)
{
    var result = await _service.GetPagedAsync(page, pageSize);
    return Ok(result);
}

public class PagedResult<T>
{
    public List<T> Items { get; set; }
    public int TotalCount { get; set; }
    public int Page { get; set; }
    public int PageSize { get; set; }
}
```

---

## Validation

```csharp
public class CreateUserDto
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }
    
    [Required]
    [StringLength(100, MinimumLength = 2)]
    public string Name { get; set; }
}
```

---

## Error Handling

```csharp
// Global exception handler
app.UseExceptionHandler(app =>
{
    app.Run(async context =>
    {
        context.Response.StatusCode = 500;
        await context.Response.WriteAsJsonAsync(new ApiResponse<object>
        {
            Success = false,
            Error = "An error occurred"
        });
    });
});
```

---

## Versioning

```csharp
[ApiController]
[Route("api/v{version:apiVersion}/[controller]")]
[ApiVersion("1.0")]
public class UsersController : ControllerBase
```

---

## DO / DON'T

| ✅ Do | ❌ Don't |
|-------|---------|
| Consistent responses | Mixed formats |
| Proper status codes | 200 for everything |
| Validate input | Trust user data |
| Version APIs | Breaking changes |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tridentsof) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

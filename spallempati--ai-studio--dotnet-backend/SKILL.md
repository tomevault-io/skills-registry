---
name: dotnet-backend
description: | Use when this capability is needed.
metadata:
  author: spallempati
---

# .NET Backend Development Standards

## Core Requirements

- **MUST** target .NET 8 LTS or .NET 10 for new projects
- **MUST** enable nullable reference types and implicit usings
- **MUST** run `dotnet format` or CSharpier after code changes
- **SHOULD** upgrade .NET 6 or earlier to at least .NET 8

## Naming Conventions

| Element | Convention | Example |
|---------|------------|---------|
| Classes/Interfaces | `PascalCase` | `UserService`, `IUserRepository` |
| Methods/Properties | `PascalCase` | `GetUserById`, `IsActive` |
| Local variables/parameters | `camelCase` | `userId`, `isValid` |
| Private fields | `_camelCase` | `_repository`, `_logger` |
| Constants | `PascalCase` | `MaxRetryCount` |
| Namespaces | `PascalCase` | Match folder structure |

- Names **MUST** be descriptive and express intent clearly
- **SHOULD NOT** use abbreviations or Hungarian notation

## Modern C# Features

| Version | Key Features to Use |
|---------|---------------------|
| C# 8.0+ | Nullable reference types, `switch` expressions, `using` declarations, async streams |
| C# 9.0+ | `record` types for DTOs, init-only setters, pattern matching |
| C# 10.0+ | File-scoped namespaces (required), global usings, `record struct` |
| C# 11.0+ | Required members, raw string literals, list patterns |
| C# 12.0+ | Primary constructors for DI, collection expressions `[1, 2, 3]` |

## Code Structure

- Methods **MUST** focus on single responsibility
- Methods **SHOULD NOT** exceed 5 parameters - use objects/builders
- **MUST** remove duplicated code blocks
- **MUST** replace magic numbers with named constants
- **SHOULD** simplify nested logic - avoid deep `if` trees
- **SHOULD** favor immutable objects (`readonly` where possible)
- **SHOULD** use `var` when type is clear from right-hand side

## Error Handling and Resources

- **MUST** use `using`/`await using` for disposable resources
- **MUST** handle exceptions meaningfully - no empty `catch` blocks
- **SHOULD** catch specific exception types, not base `Exception`
- **SHOULD** create custom exceptions for domain-specific errors
- **SHOULD** use `ArgumentNullException.ThrowIfNull()` for null checks

## Dependency Injection

- **MUST** use constructor injection for all dependencies
- **MUST** declare dependency fields as `private readonly`
- **MUST NOT** use `IServiceProvider` directly in business logic
- **SHOULD** use `IOptions<T>` for type-safe configuration

```csharp
public class UserService(
    IUserRepository repository,
    IOptions<UserSettings> settings,
    ILogger<UserService> logger)
{
    public async Task<User?> GetUserAsync(int id)
    {
        var user = await repository.GetByIdAsync(id);
        if (user is null)
            logger.LogWarning("User {UserId} not found", id);
        return user;
    }
}
```

## Data Access (Entity Framework Core or Dapper)

- **MUST** use EF Core or Dapper for database operations
- **MUST** use parameterized queries to prevent SQL injection
- **MUST** use appropriate loading strategies to avoid N+1 queries (when using EF Core):
  - `.Include()` for eager loading
  - Split queries for complex includes
  - `.Select()` projections when full entities aren't needed
- **SHOULD** use `AsNoTracking()` for read-only queries (EF Core)
- **SHOULD NOT** use raw SQL when LINQ methods suffice
- **MUST NOT** expose EF Core entities directly in API responses

## Logging

- **MUST** use `ILogger<T>` for all logging
- **MUST** use structured logging with message templates
- **MUST NOT** use `Console.WriteLine()` for logging

```csharp
_logger.LogInformation("Processing order {OrderId} for user {UserId}", orderId, userId);
_logger.LogError(ex, "Failed to process order {OrderId}", orderId);
```

## Validation

- **MUST** validate all external input at application boundaries
- **MUST** use FluentValidation or Data Annotations
- **MUST** sanitize user inputs before use

```csharp
public class CreateUserValidator : AbstractValidator<CreateUserRequest>
{
    public CreateUserValidator()
    {
        RuleFor(x => x.Email).NotEmpty().EmailAddress();
        RuleFor(x => x.Name).NotEmpty().MaximumLength(100);
    }
}
```

## API Development

- **MUST** use RESTful conventions
- **MUST** version API endpoints (e.g., `/api/v1/users`)
- **MUST** use `[ApiController]` attribute for REST controllers
- **MUST** implement proper error handling with `ProblemDetails` (RFC 7807)
- **MUST** use DTOs for request/response objects
- **MUST** document APIs using Swagger/OpenAPI

### HTTP Status Codes

| Scenario | Status Code |
|----------|-------------|
| Successful GET/PUT | 200 OK |
| Successful POST | 201 Created |
| Successful DELETE | 204 No Content |
| Validation error | 422 Unprocessable Entity |
| Missing resource | 404 Not Found |
| Unhandled exception | 500 Internal Server Error |

```csharp
[ApiController]
[Route("api/[controller]")]
public class UsersController(IUserService userService) : ControllerBase
{
    [HttpGet("{id}")]
    [ProducesResponseType<UserDto>(StatusCodes.Status200OK)]
    [ProducesResponseType<ProblemDetails>(StatusCodes.Status404NotFound)]
    public async Task<ActionResult<UserDto>> GetUser(int id)
    {
        var user = await userService.GetUserAsync(id);
        return user is null ? NotFound() : Ok(user);
    }
}
```

## Testing

- **MUST** write unit tests for all public methods
- **MUST** follow Arrange-Act-Assert (AAA) pattern
- **MUST** mock external dependencies using Moq, NSubstitute, or FakeItEasy
- **MUST** test error scenarios and edge cases
- **SHOULD** use xUnit as testing framework
- **SHOULD** use `WebApplicationFactory<T>` for integration tests

```csharp
public class UserServiceTests
{
    private readonly Mock<IUserRepository> _mockRepo = new();
    private readonly UserService _service;

    public UserServiceTests() =>
        _service = new UserService(_mockRepo.Object, Options.Create(new UserSettings()), Mock.Of<ILogger<UserService>>());

    [Fact]
    public async Task GetUserAsync_WhenExists_ReturnsUser()
    {
        // Arrange
        var expected = new User { Id = 1, Name = "Test" };
        _mockRepo.Setup(r => r.GetByIdAsync(1)).ReturnsAsync(expected);

        // Act
        var result = await _service.GetUserAsync(1);

        // Assert
        Assert.Equal(expected, result);
    }
}
```

## Security

- **MUST** use parameterized queries (prevent SQL injection)
- **MUST** implement proper authentication/authorization
- **MUST** use HTTPS for all production communications
- **MUST** store secrets using User Secrets or Azure Key Vault
- **MUST NOT** log passwords or tokens
- **SHOULD** implement proper CORS configuration

## Performance

- **SHOULD** use caching (`IMemoryCache` or distributed cache) when appropriate
- **SHOULD** use async processing for non-blocking operations
- **SHOULD** enable response compression for APIs when appropriate
- **MUST** implement pagination for large datasets
- **SHOULD** use `Span<T>` and `Memory<T>` for buffer operations
- **SHOULD** use `StringBuilder` for string concatenation in loops

## Anti-Patterns to Avoid

| Anti-Pattern | Correct Approach |
|--------------|------------------|
| `.Result` or `.Wait()` on async | Use `await` (prevents deadlocks) |
| `Task.Run()` in ASP.NET requests | Use proper async patterns |
| `catch (Exception)` without handling | Catch specific types, log, rethrow |
| `IServiceProvider` in business logic | Use constructor injection |
| Exposing EF entities in APIs | Use DTOs |
| `Console.WriteLine()` | Use `ILogger<T>` |
| `dynamic` type | Use proper typing |
| `==` for value type comparison | Use `.Equals()` |
| Missing `GetHashCode()` | Implement when overriding `Equals()` |
| Ignoring compiler warnings | Fix all warnings |

## Configuration

- **MUST** use `appsettings.json` for externalized configuration (or Azure Key Vault / App Configuration for enterprise settings)
- **SHOULD** use `appsettings.{Environment}.json` for environment-specific settings when not using Key Vault / App Configuration
- **SHOULD** use `Directory.Build.props` for centralized package versions
- **MUST** keep dependencies up to date and audit for vulnerabilities

**Note**: For enterprise applications, prefer Azure Key Vault and App Configuration over `appsettings.{Environment}.json` files for environment-specific and sensitive configuration.

---

All .NET projects **MUST** adhere to these standards for consistency, maintainability, and security.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spallempati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

---
name: integration-test-contracts
description: | Use when this capability is needed.
metadata:
  author: spallempati
---

# Require Integration Test Contract Validation

## Description

This rule enforces that every service includes integration tests which validate
its interface and data contracts. These tests must execute against a realistic
environment (e.g., in-memory DB, test database instance, or optionally Dockerized DB)
and assert correctness of API schemas, database migrations, and service behaviors.

## Purpose

To ensure that interfaces are stable, expected behaviors are preserved across
changes, and that breaking schema drifts are caught early. This rule reinforces
backward compatibility, testable API definitions, and confidence in CI/CD
automation.

## Scope

- Microservices with REST APIs or direct database access
- Integration test classes, API contract schemas, and migration scripts
- Java (JUnit), .NET (xUnit, `WebApplicationFactory<T>`)
- Applies to CI pipelines and local developer environments
- Developers, testers, and DevOps

## SDLC Integration

- **Planning**: Integration testing requirements defined per story
- **Analysis**: API and schema impact reviewed for coverage
- **Design**: Contracts formalized (e.g., OpenAPI, DB changelog)
- **Development**: Tests run against test databases or in-memory providers
- **Testing**: Tests assert expected responses and schema states
- **Deployment**: Verifies API/data compatibility in CD pipelines
- **Maintenance**: Prevents undetected breaking changes in shared contracts

## Standards

### API & Schema Test Requirements

- Services **MUST** include integration tests validating REST or database
  contract behavior
- Tests **MUST** run in a representative environment (e.g., test DB instance, in-memory provider, or optionally Docker containers)
- Schema drift between expected and applied migrations **MUST NOT** occur
- CI pipelines **MUST** report integration test pass rate ≥ 99% on every PR

**Note**: TestContainers can be used optionally for Docker-based testing environments
but is not required. Use `WebApplicationFactory<T>` for ASP.NET Core integration tests,
and in-memory databases or test database instances as appropriate.

## Actionable Metrics

| Metric                     | Target Value      | Measurement Method                    | Enforcement Level |
| -------------------------- | ----------------- | ------------------------------------- | ----------------- |
| Integration test pass rate | ≥ 99 % per PR     | CI test suite log                     | **MUST**          |
| Schema drift incidents     | 0                 | Migration diff validator              | **MUST**          |

## Implementation

### Configuration Requirements

- Use test database instances, in-memory databases, or optionally Docker Compose/TestContainers
- Validate schema post-migration (Flyway, Liquibase, EF Core migrations, etc.)
- Include contract validation using JSON schema or snapshot testing

#### Example: Correct Implementation (Java)

```java
// JUnit + Testcontainers integration test
@Container
static MySQLContainer<?> mysql = new MySQLContainer<>("mysql:8.0");

@Test
void appliesMigrationsAndCreatesUserTierColumn() {
    jdbcTemplate.execute("ALTER TABLE users ADD COLUMN user_tier VARCHAR(50)");
    assertTrue(columnExists("users", "user_tier"));
}
```

#### Example: Correct Implementation (C#/.NET with Testcontainers)

```csharp
// xUnit + Testcontainers.NET integration test
public class UserRepositoryIntegrationTests : IAsyncLifetime
{
    private readonly MsSqlContainer _sqlContainer = new MsSqlBuilder()
        .WithImage("mcr.microsoft.com/mssql/server:2022-latest")
        .Build();

    private UserRepository _repository = null!;

    public async Task InitializeAsync()
    {
        await _sqlContainer.StartAsync();
        var connectionString = _sqlContainer.GetConnectionString();
        var context = new AppDbContext(
            new DbContextOptionsBuilder<AppDbContext>()
                .UseSqlServer(connectionString)
                .Options);
        await context.Database.MigrateAsync();
        _repository = new UserRepository(context);
    }

    public async Task DisposeAsync() => await _sqlContainer.DisposeAsync();

    [Fact]
    public async Task CreateUser_ValidData_PersistsToDatabase()
    {
        // Arrange
        var user = new User { Name = "Test User", Email = "test@example.com" };

        // Act
        var created = await _repository.CreateAsync(user);
        var retrieved = await _repository.GetByIdAsync(created.Id);

        // Assert
        Assert.NotNull(retrieved);
        Assert.Equal("Test User", retrieved.Name);
    }
}
```

### WebApplicationFactory for ASP.NET Core

```csharp
public class ApiIntegrationTests : IClassFixture<WebApplicationFactory<Program>>
{
    private readonly HttpClient _client;

    public ApiIntegrationTests(WebApplicationFactory<Program> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetUser_ExistingId_ReturnsUser()
    {
        // Act
        var response = await _client.GetAsync("/api/users/1");

        // Assert
        response.EnsureSuccessStatusCode();
        var user = await response.Content.ReadFromJsonAsync<UserDto>();
        Assert.NotNull(user);
    }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spallempati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

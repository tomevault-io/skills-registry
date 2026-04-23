---
name: xunit-testing
description: description: xUnit testing for .NET applications Use when this capability is needed.
metadata:
  author: tridentsof
---
---
name: xunit-testing
description: xUnit testing for .NET applications
---

# xUnit Testing

> Unit and integration testing for C#/.NET.

---

## Basic Test

```csharp
public class CalculatorTests
{
    [Fact]
    public void Add_TwoNumbers_ReturnsSum()
    {
        // Arrange
        var calculator = new Calculator();
        
        // Act
        var result = calculator.Add(2, 3);
        
        // Assert
        Assert.Equal(5, result);
    }
}
```

---

## Theory with Data

```csharp
[Theory]
[InlineData(2, 3, 5)]
[InlineData(-1, 1, 0)]
[InlineData(0, 0, 0)]
public void Add_VariousInputs_ReturnsExpected(int a, int b, int expected)
{
    var result = new Calculator().Add(a, b);
    Assert.Equal(expected, result);
}
```

---

## Async Tests

```csharp
[Fact]
public async Task GetUser_ValidId_ReturnsUser()
{
    // Arrange
    var service = CreateService();
    
    // Act
    var result = await service.GetByIdAsync(1);
    
    // Assert
    Assert.NotNull(result);
    Assert.Equal(1, result.Id);
}
```

---

## Mocking with Moq

```csharp
[Fact]
public async Task GetUser_CallsRepository()
{
    // Arrange
    var mockRepo = new Mock<IUserRepository>();
    mockRepo.Setup(r => r.GetByIdAsync(1))
        .ReturnsAsync(new User { Id = 1 });
    
    var service = new UserService(mockRepo.Object);
    
    // Act
    var result = await service.GetByIdAsync(1);
    
    // Assert
    mockRepo.Verify(r => r.GetByIdAsync(1), Times.Once);
}
```

---

## Test Fixtures

```csharp
public class DatabaseFixture : IDisposable
{
    public DbContext Context { get; }
    
    public DatabaseFixture()
    {
        Context = CreateTestContext();
    }
    
    public void Dispose() => Context.Dispose();
}

public class UserTests : IClassFixture<DatabaseFixture>
{
    private readonly DatabaseFixture _fixture;
    
    public UserTests(DatabaseFixture fixture)
    {
        _fixture = fixture;
    }
}
```

---

## Run Tests

```bash
dotnet test
dotnet test --filter "FullyQualifiedName~UserTests"
dotnet test --collect:"XPlat Code Coverage"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tridentsof) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

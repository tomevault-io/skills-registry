---
name: testing
description: Testing workflows for .NET with xUnit, Moq, and FluentAssertions. Use this skill when writing unit tests, integration tests, mocking dependencies, or setting up test projects. Triggers on tasks involving xUnit, test creation, mocking, assertions, or test-driven development in .NET. Use when this capability is needed.
metadata:
  author: shoaibmalick
---

# Testing

A comprehensive skill for .NET testing with xUnit.

## Tech Stack

| Category | Technology |
|----------|------------|
| Framework | xUnit |
| Mocking | Moq |
| Assertions | FluentAssertions |
| Coverage | Coverlet |

## Project Structure

```
tests/
  MyApp.UnitTests/
    Controllers/
    Services/
    Repositories/
  MyApp.IntegrationTests/
    Api/
    Database/
```

## Setup

### Install Packages

```bash
dotnet add package xunit
dotnet add package xunit.runner.visualstudio
dotnet add package Microsoft.NET.Test.Sdk
dotnet add package Moq
dotnet add package FluentAssertions
dotnet add package coverlet.collector
```

## Basic Test Structure

```csharp
using Xunit;

namespace MyApp.UnitTests.Services;

public class ProductServiceTests
{
    [Fact]
    public void GetById_WithValidId_ReturnsProduct()
    {
        // Arrange
        var service = new ProductService();

        // Act
        var result = service.GetById(1);

        // Assert
        Assert.NotNull(result);
        Assert.Equal(1, result.Id);
    }

    [Fact]
    public void GetById_WithInvalidId_ReturnsNull()
    {
        // Arrange
        var service = new ProductService();

        // Act
        var result = service.GetById(-1);

        // Assert
        Assert.Null(result);
    }
}
```

## Parameterized Tests

```csharp
public class CalculatorTests
{
    [Theory]
    [InlineData(2, 3, 5)]
    [InlineData(0, 0, 0)]
    [InlineData(-1, 1, 0)]
    public void Add_ReturnsCorrectSum(int a, int b, int expected)
    {
        var calculator = new Calculator();
        var result = calculator.Add(a, b);
        Assert.Equal(expected, result);
    }

    [Theory]
    [MemberData(nameof(GetTestData))]
    public void Multiply_ReturnsCorrectProduct(int a, int b, int expected)
    {
        var calculator = new Calculator();
        var result = calculator.Multiply(a, b);
        Assert.Equal(expected, result);
    }

    public static IEnumerable<object[]> GetTestData()
    {
        yield return new object[] { 2, 3, 6 };
        yield return new object[] { 0, 5, 0 };
        yield return new object[] { -2, 3, -6 };
    }
}
```

## Mocking with Moq

```csharp
using Moq;

public class ProductServiceTests
{
    private readonly Mock<IProductRepository> _mockRepository;
    private readonly ProductService _service;

    public ProductServiceTests()
    {
        _mockRepository = new Mock<IProductRepository>();
        _service = new ProductService(_mockRepository.Object);
    }

    [Fact]
    public async Task GetAllAsync_ReturnsAllProducts()
    {
        // Arrange
        var products = new List<Product>
        {
            new Product { Id = 1, Name = "Product 1" },
            new Product { Id = 2, Name = "Product 2" }
        };
        _mockRepository.Setup(r => r.GetAllAsync())
            .ReturnsAsync(products);

        // Act
        var result = await _service.GetAllAsync();

        // Assert
        Assert.Equal(2, result.Count());
        _mockRepository.Verify(r => r.GetAllAsync(), Times.Once);
    }

    [Fact]
    public async Task CreateAsync_CallsRepository()
    {
        // Arrange
        var dto = new CreateProductDto("New Product", 99.99m);
        _mockRepository.Setup(r => r.AddAsync(It.IsAny<Product>()))
            .Returns(Task.CompletedTask);

        // Act
        await _service.CreateAsync(dto);

        // Assert
        _mockRepository.Verify(r => r.AddAsync(
            It.Is<Product>(p => p.Name == "New Product")), Times.Once);
    }
}
```

## FluentAssertions

```csharp
using FluentAssertions;

public class ProductServiceTests
{
    [Fact]
    public void GetById_WithValidId_ReturnsCorrectProduct()
    {
        // Arrange
        var service = new ProductService();

        // Act
        var result = service.GetById(1);

        // Assert
        result.Should().NotBeNull();
        result.Id.Should().Be(1);
        result.Name.Should().NotBeNullOrEmpty();
        result.Price.Should().BeGreaterThan(0);
    }

    [Fact]
    public void GetAll_ReturnsNonEmptyList()
    {
        var service = new ProductService();
        var result = service.GetAll();

        result.Should().NotBeEmpty()
            .And.HaveCountGreaterThan(0)
            .And.OnlyContain(p => p.Id > 0);
    }

    [Fact]
    public void Create_ThrowsException_WhenNameIsEmpty()
    {
        var service = new ProductService();

        var act = () => service.Create(new CreateProductDto("", 10));

        act.Should().Throw<ValidationException>()
            .WithMessage("*Name*required*");
    }
}
```

## Async Testing

```csharp
public class AsyncServiceTests
{
    [Fact]
    public async Task GetDataAsync_ReturnsData()
    {
        // Arrange
        var service = new DataService();

        // Act
        var result = await service.GetDataAsync();

        // Assert
        result.Should().NotBeNull();
    }

    [Fact]
    public async Task GetDataAsync_ThrowsException_WhenTimeout()
    {
        var service = new DataService();

        await Assert.ThrowsAsync<TimeoutException>(
            () => service.GetDataAsync(timeout: 1));
    }
}
```

## Test Fixtures (Shared Context)

```csharp
public class DatabaseFixture : IDisposable
{
    public AppDbContext Context { get; }

    public DatabaseFixture()
    {
        var options = new DbContextOptionsBuilder<AppDbContext>()
            .UseInMemoryDatabase("TestDb")
            .Options;
        Context = new AppDbContext(options);
        SeedData();
    }

    private void SeedData()
    {
        Context.Products.AddRange(
            new Product { Id = 1, Name = "Test Product" }
        );
        Context.SaveChanges();
    }

    public void Dispose()
    {
        Context.Dispose();
    }
}

public class ProductRepositoryTests : IClassFixture<DatabaseFixture>
{
    private readonly DatabaseFixture _fixture;

    public ProductRepositoryTests(DatabaseFixture fixture)
    {
        _fixture = fixture;
    }

    [Fact]
    public async Task GetAll_ReturnsSeededProducts()
    {
        var repository = new ProductRepository(_fixture.Context);
        var result = await repository.GetAllAsync();
        result.Should().NotBeEmpty();
    }
}
```

## Common Commands

```bash
# Run all tests
dotnet test

# Run with coverage
dotnet test --collect:"XPlat Code Coverage"

# Run specific test project
dotnet test tests/MyApp.UnitTests

# Run specific test class
dotnet test --filter "FullyQualifiedName~ProductServiceTests"

# Run specific test method
dotnet test --filter "FullyQualifiedName~GetById_WithValidId"

# Verbose output
dotnet test --logger "console;verbosity=detailed"
```

## References

For detailed guides, see:
- references/mocking-guide.md - Advanced mocking patterns
- references/integration-tests.md - Integration testing patterns
- references/test-patterns.md - Common testing patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shoaibmalick) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

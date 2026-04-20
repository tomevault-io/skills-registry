---
name: backend-api
description: Backend API development workflows for .NET Core with C#. Use this skill when creating REST APIs, controllers, services, repositories, Entity Framework Core models, or implementing authentication. Triggers on tasks involving .NET Core, ASP.NET Core, Web API, C# backend development, or database operations. Use when this capability is needed.
metadata:
  author: shoaibmalick
---

# Backend API

A comprehensive skill for .NET Core / C# backend API development.

## Tech Stack

| Category | Technology |
|----------|------------|
| Framework | ASP.NET Core 8+ |
| Language | C# |
| ORM | Entity Framework Core |
| Database | SQL Server / PostgreSQL |
| Auth | JWT / Identity |

## Project Structure

```
src/
  MyApp.Api/
    Controllers/        # API endpoints
    Program.cs          # App entry point
  MyApp.Application/
    Services/           # Business logic
    DTOs/               # Data transfer objects
    Interfaces/         # Service contracts
  MyApp.Domain/
    Entities/           # Domain models
    Enums/              # Enumerations
  MyApp.Infrastructure/
    Data/               # DbContext, configurations
    Repositories/       # Data access
    Migrations/         # EF migrations
```

## Controller Pattern

```csharp
using Microsoft.AspNetCore.Mvc;

namespace MyApp.Api.Controllers;

[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    private readonly IProductService _productService;

    public ProductsController(IProductService productService)
    {
        _productService = productService;
    }

    [HttpGet]
    public async Task<ActionResult<IEnumerable<ProductDto>>> GetAll()
    {
        var products = await _productService.GetAllAsync();
        return Ok(products);
    }

    [HttpGet("{id}")]
    public async Task<ActionResult<ProductDto>> GetById(int id)
    {
        var product = await _productService.GetByIdAsync(id);
        if (product == null) return NotFound();
        return Ok(product);
    }

    [HttpPost]
    public async Task<ActionResult<ProductDto>> Create(CreateProductDto dto)
    {
        var product = await _productService.CreateAsync(dto);
        return CreatedAtAction(nameof(GetById), new { id = product.Id }, product);
    }

    [HttpPut("{id}")]
    public async Task<IActionResult> Update(int id, UpdateProductDto dto)
    {
        await _productService.UpdateAsync(id, dto);
        return NoContent();
    }

    [HttpDelete("{id}")]
    public async Task<IActionResult> Delete(int id)
    {
        await _productService.DeleteAsync(id);
        return NoContent();
    }
}
```

## Service Pattern

```csharp
namespace MyApp.Application.Services;

public interface IProductService
{
    Task<IEnumerable<ProductDto>> GetAllAsync();
    Task<ProductDto?> GetByIdAsync(int id);
    Task<ProductDto> CreateAsync(CreateProductDto dto);
    Task UpdateAsync(int id, UpdateProductDto dto);
    Task DeleteAsync(int id);
}

public class ProductService : IProductService
{
    private readonly IProductRepository _repository;

    public ProductService(IProductRepository repository)
    {
        _repository = repository;
    }

    public async Task<IEnumerable<ProductDto>> GetAllAsync()
    {
        var products = await _repository.GetAllAsync();
        return products.Select(p => new ProductDto(p.Id, p.Name, p.Price));
    }

    public async Task<ProductDto?> GetByIdAsync(int id)
    {
        var product = await _repository.GetByIdAsync(id);
        return product == null ? null : new ProductDto(product.Id, product.Name, product.Price);
    }

    public async Task<ProductDto> CreateAsync(CreateProductDto dto)
    {
        var product = new Product { Name = dto.Name, Price = dto.Price };
        await _repository.AddAsync(product);
        return new ProductDto(product.Id, product.Name, product.Price);
    }

    public async Task UpdateAsync(int id, UpdateProductDto dto)
    {
        var product = await _repository.GetByIdAsync(id)
            ?? throw new NotFoundException("Product not found");
        product.Name = dto.Name;
        product.Price = dto.Price;
        await _repository.UpdateAsync(product);
    }

    public async Task DeleteAsync(int id)
    {
        await _repository.DeleteAsync(id);
    }
}
```

## Entity and DTO Pattern

```csharp
// Domain Entity
namespace MyApp.Domain.Entities;

public class Product
{
    public int Id { get; set; }
    public string Name { get; set; } = string.Empty;
    public decimal Price { get; set; }
    public DateTime CreatedAt { get; set; }
    public DateTime? UpdatedAt { get; set; }
}

// DTOs
namespace MyApp.Application.DTOs;

public record ProductDto(int Id, string Name, decimal Price);
public record CreateProductDto(string Name, decimal Price);
public record UpdateProductDto(string Name, decimal Price);
```

## Repository Pattern

```csharp
namespace MyApp.Infrastructure.Repositories;

public interface IProductRepository
{
    Task<IEnumerable<Product>> GetAllAsync();
    Task<Product?> GetByIdAsync(int id);
    Task AddAsync(Product product);
    Task UpdateAsync(Product product);
    Task DeleteAsync(int id);
}

public class ProductRepository : IProductRepository
{
    private readonly AppDbContext _context;

    public ProductRepository(AppDbContext context)
    {
        _context = context;
    }

    public async Task<IEnumerable<Product>> GetAllAsync()
        => await _context.Products.ToListAsync();

    public async Task<Product?> GetByIdAsync(int id)
        => await _context.Products.FindAsync(id);

    public async Task AddAsync(Product product)
    {
        _context.Products.Add(product);
        await _context.SaveChangesAsync();
    }

    public async Task UpdateAsync(Product product)
    {
        _context.Products.Update(product);
        await _context.SaveChangesAsync();
    }

    public async Task DeleteAsync(int id)
    {
        var product = await GetByIdAsync(id);
        if (product != null)
        {
            _context.Products.Remove(product);
            await _context.SaveChangesAsync();
        }
    }
}
```

## DbContext Setup

```csharp
namespace MyApp.Infrastructure.Data;

public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }

    public DbSet<Product> Products => Set<Product>();

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.ApplyConfigurationsFromAssembly(typeof(AppDbContext).Assembly);
    }
}
```

## Dependency Injection (Program.cs)

```csharp
var builder = WebApplication.CreateBuilder(args);

// Add DbContext
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));

// Add Services
builder.Services.AddScoped<IProductService, ProductService>();
builder.Services.AddScoped<IProductRepository, ProductRepository>();

// Add Controllers
builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();
app.Run();
```

## Common Commands

```bash
# Create new project
dotnet new webapi -n MyApp.Api
dotnet new classlib -n MyApp.Application
dotnet new classlib -n MyApp.Domain
dotnet new classlib -n MyApp.Infrastructure

# Run application
dotnet run --project src/MyApp.Api

# EF Core Migrations
dotnet ef migrations add InitialCreate --project src/MyApp.Infrastructure --startup-project src/MyApp.Api
dotnet ef database update --project src/MyApp.Infrastructure --startup-project src/MyApp.Api

# Build and Test
dotnet build
dotnet test
```

## References

For detailed guides, see:
- references/ef-core-guide.md - Entity Framework Core patterns
- references/auth-guide.md - Authentication and Authorization
- references/validation-guide.md - Request validation patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shoaibmalick) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

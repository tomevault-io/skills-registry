---
name: dotnet-vertical-slice
description: .NET 10 web APIs using vertical slice architecture with minimal APIs. Use when building APIs organized by feature rather than technical layers, with Result pattern for error handling. No MediatR, no AutoMapper. Use when this capability is needed.
metadata:
  author: neversight
---

# .NET 10 Vertical Slice Architecture

Organize code by feature, not by layer. Each feature is self-contained with its endpoint, request/response, validation, and handler in a single file.

## Project Structure

```
src/
├── Features/
│   ├── Products/
│   │   ├── GetProduct.cs
│   │   ├── CreateProduct.cs
│   │   └── ProductMapper.cs
│   └── Orders/
│       └── ...
├── Shared/
│   ├── Results/
│   │   ├── Result.cs
│   │   └── Error.cs
│   └── Validation/
│       └── ValidationResult.cs
├── Entities/
└── Program.cs
```

## Feature Slice Pattern

One file per operation containing everything needed:

```csharp
// Features/Products/CreateProduct.cs
public static class CreateProduct
{
    public sealed record Request(string Name, decimal Price);
    public sealed record Response(int Id, string Name, decimal Price);

    public static async Task<Result<Response>> HandleAsync(
        Request request, AppDbContext db, CancellationToken ct)
    {
        var validation = Validate(request);
        if (!validation.IsValid)
            return validation.ToResult<Response>(null!);

        var product = new Product { Name = request.Name, Price = request.Price };
        db.Products.Add(product);
        await db.SaveChangesAsync(ct);

        return new Response(product.Id, product.Name, product.Price);
    }

    private static ValidationResult Validate(Request request) =>
        ValidationExtensions.Validate()
            .NotEmpty(request.Name, "Name")
            .GreaterThan(request.Price, 0, "Price");

    public static void MapEndpoint(IEndpointRouteBuilder app) => app
        .MapPost("/api/products", async (Request request, AppDbContext db, CancellationToken ct) =>
            (await HandleAsync(request, db, ct)).ToCreatedResponse(r => $"/api/products/{r.Id}"))
        .WithName("CreateProduct")
        .WithTags("Products");
}
```

## Core Principles

1. **Result pattern only** - Never throw exceptions, return `Result<T>` or `Result`
2. **Static handlers** - Use `public static async Task<Result<T>> HandleAsync(...)`
3. **Inline validation** - Validate at handler start, return early on failure
4. **Manual mapping** - Use extension methods in `*Mapper.cs` files
5. **Projections** - Use `.Select()` for queries, avoid loading full entities

## References

See detailed implementations in the `references/` folder:

- [Result Pattern](references/result-pattern.md) - Error types, Result class, HTTP mapping
- [Validation](references/validation.md) - ValidationResult, fluent extensions
- [Feature Examples](references/feature-examples.md) - CRUD, filtering, pagination

## Guidelines

- One feature = one file (endpoint + request/response + validation + handler)
- Name files by operation: `CreateProduct.cs`, `GetProducts.cs`
- Keep entities in shared folder (only cross-cutting concern)
- Use `[AsParameters]` for query parameters
- Group endpoints with `.WithTags()` for OpenAPI

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

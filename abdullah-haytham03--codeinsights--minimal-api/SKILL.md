---
name: minimal-api-expert
description: Build ASP.NET Core Minimal APIs with .NET 10 best practices Use when this capability is needed.
metadata:
  author: abdullah-haytham03
---

You are a Minimal API expert specializing in ASP.NET Core 10 backend-first web applications.

## Your Expertise
- ASP.NET Core Minimal API architecture
- Route grouping and endpoint design
- Request and response modeling
- Validation and error handling
- Dependency injection in Minimal APIs
- Filters and middleware
- API versioning and conventions
- SQLite-backed applications
- Hosting-ready API design

## Minimal API Best Practices

### Program Structure
```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();
builder.Services.AddSqlite<AppDbContext>("Data Source=app.db");
builder.Services.AddScoped<IOrderService, OrderService>();

var app = builder.Build();

app.UseExceptionHandler("/error");
app.UseHttpsRedirection();

app.MapOrderEndpoints();

app.Run();
```

---

### Route Grouping
```csharp
public static class OrderEndpoints
{
    public static RouteGroupBuilder MapOrderEndpoints(this IEndpointRouteBuilder app)
    {
        var group = app.MapGroup("/api/orders")
            .WithTags("Orders");

        group.MapGet("/", GetOrders);
        group.MapGet("/{id:guid}", GetOrderById);
        group.MapPost("/", CreateOrder);
        group.MapPut("/{id:guid}", UpdateOrder);
        group.MapDelete("/{id:guid}", DeleteOrder);

        return group;
    }
}
```

---

### Endpoint Handlers
```csharp
static async Task<IResult> GetOrders(
    IOrderService orderService,
    [FromQuery] string? search,
    [FromQuery] int page = 1)
{
    var orders = await orderService.GetOrdersAsync(search, page);
    return Results.Ok(orders);
}

static async Task<IResult> GetOrderById(
    Guid id,
    IOrderService orderService)
{
    var order = await orderService.GetByIdAsync(id);
    return order is not null
        ? Results.Ok(order)
        : Results.NotFound();
}
```

---

### Request Models with Validation
```csharp
public sealed class CreateOrderRequest
{
    [Required]
    public Guid CustomerId { get; init; }

    [Required, StringLength(500, MinimumLength = 10)]
    public string Description { get; init; } = string.Empty;

    [Range(0.01, 1_000_000)]
    public decimal TotalAmount { get; init; }

    public DateOnly? DueDate { get; init; }
}
```

---

### Manual Validation Pattern
```csharp
static async Task<IResult> CreateOrder(
    CreateOrderRequest request,
    IOrderService orderService)
{
    if (!MiniValidator.TryValidate(request, out var errors))
        return Results.ValidationProblem(errors);

    var id = await orderService.CreateAsync(request);
    return Results.Created($"/api/orders/{id}", new { id });
}
```

---

### Consistent Error Handling
```csharp
app.Map("/error", (HttpContext context) =>
{
    var exception = context.Features.Get<IExceptionHandlerFeature>()?.Error;

    return Results.Problem(
        title: "An unexpected error occurred",
        detail: exception?.Message,
        statusCode: StatusCodes.Status500InternalServerError
    );
});
```

---

### Endpoint Filters
```csharp
public class RequireApiKeyFilter : IEndpointFilter
{
    public async ValueTask<object?> InvokeAsync(
        EndpointFilterInvocationContext context,
        EndpointFilterDelegate next)
    {
        var httpContext = context.HttpContext;

        if (!httpContext.Request.Headers.TryGetValue("X-API-KEY", out _))
            return Results.Unauthorized();

        return await next(context);
    }
}
```

Usage:
```csharp
group.MapPost("/", CreateOrder)
     .AddEndpointFilter<RequireApiKeyFilter>();
```

---

### SQLite DbContext
```csharp
public sealed class AppDbContext : DbContext
{
    public DbSet<Order> Orders => Set<Order>();

    public AppDbContext(DbContextOptions<AppDbContext> options)
        : base(options) { }
}
```

---

### Recommended Folder Structure
```
src/
├── Program.cs
├── Endpoints/
│   ├── OrderEndpoints.cs
│   └── ProjectEndpoints.cs
├── Contracts/
│   ├── CreateOrderRequest.cs
│   └── UpdateOrderRequest.cs
├── Services/
│   ├── IOrderService.cs
│   └── OrderService.cs
├── Data/
│   └── AppDbContext.cs
├── Domain/
│   └── Order.cs
└── Infrastructure/
    ├── Filters/
    └── Middleware/
```

---

## Security & Validation
```csharp
builder.Services.AddAntiforgery();

app.Use(async (context, next) =>
{
    if (HttpMethods.IsPost(context.Request.Method))
        await context.RequestServices
            .GetRequiredService<IAntiforgery>()
            .ValidateRequestAsync(context);

    await next();
});
```

---

## Checklist
- [ ] Endpoints grouped logically
- [ ] No business logic in endpoint handlers
- [ ] Request models separate from domain models
- [ ] Explicit validation for all inputs
- [ ] Consistent error responses
- [ ] Dependency injection via parameters
- [ ] SQLite-friendly data access
- [ ] Minimal middleware usage
- [ ] Clear separation of concerns
- [ ] Hosting-ready configuration

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/abdullah-haytham03) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

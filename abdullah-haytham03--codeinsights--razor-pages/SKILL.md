---
name: razor-pages-expert
description: Build ASP.NET Core Razor Pages with .NET 10 best practices Use when this capability is needed.
metadata:
  author: abdullah-haytham03
---

You are a Razor Pages expert specializing in ASP.NET Core 10 server-rendered web applications.

## Your Expertise
- Razor Pages architecture and conventions
- Page models and handlers
- Tag Helpers and view components
- Form handling and validation
- Partial views and layouts

## Razor Pages Best Practices

### Page Model Structure
```csharp
public class OrdersModel : PageModel
{
    private readonly IOrderService _orderService;
    private readonly ILogger<OrdersModel> _logger;

    public OrdersModel(IOrderService orderService, ILogger<OrdersModel> logger)
    {
        _orderService = orderService;
        _logger = logger;
    }

    public IReadOnlyList<OrderViewModel> Orders { get; private set; } = [];

    [BindProperty(SupportsGet = true)]
    public string? SearchTerm { get; set; }

    [BindProperty(SupportsGet = true)]
    public int PageNumber { get; set; } = 1;

    public async Task<IActionResult> OnGetAsync()
    {
        Orders = await _orderService.GetOrdersAsync(SearchTerm, PageNumber);
        return Page();
    }
}
```

### Form Handling with Multiple Handlers
```csharp
public class EditOrderModel : PageModel
{
    [BindProperty]
    public OrderInputModel Input { get; set; } = new();

    [BindProperty]
    public Guid OrderId { get; set; }

    public async Task<IActionResult> OnGetAsync(Guid id)
    {
        var order = await _orderService.GetByIdAsync(id);
        if (order is null)
            return NotFound();

        OrderId = id;
        Input = OrderInputModel.FromOrder(order);
        return Page();
    }

    public async Task<IActionResult> OnPostSaveAsync()
    {
        if (!ModelState.IsValid)
            return Page();

        await _orderService.UpdateAsync(OrderId, Input);
        TempData["Success"] = "Order saved successfully.";
        return RedirectToPage("./Index");
    }

    public async Task<IActionResult> OnPostDeleteAsync()
    {
        await _orderService.DeleteAsync(OrderId);
        TempData["Success"] = "Order deleted.";
        return RedirectToPage("./Index");
    }

    public async Task<IActionResult> OnPostCancelAsync()
    {
        await _orderService.CancelAsync(OrderId);
        return RedirectToPage();
    }
}
```

### Input Models with Validation
```csharp
public class OrderInputModel
{
    [Required(ErrorMessage = "Customer is required")]
    [Display(Name = "Customer")]
    public Guid CustomerId { get; set; }

    [Required]
    [StringLength(500, MinimumLength = 10)]
    [Display(Name = "Description")]
    public string Description { get; set; } = string.Empty;

    [Range(0.01, 1_000_000)]
    [DataType(DataType.Currency)]
    [Display(Name = "Total Amount")]
    public decimal TotalAmount { get; set; }

    [DataType(DataType.Date)]
    [Display(Name = "Due Date")]
    public DateOnly? DueDate { get; set; }

    public static OrderInputModel FromOrder(Order order) => new()
    {
        CustomerId = order.CustomerId,
        Description = order.Description,
        TotalAmount = order.TotalAmount,
        DueDate = order.DueDate
    };
}
```

### Razor View Best Practices
```html
@page "{id:guid}"
@model EditOrderModel
@{
    ViewData["Title"] = "Edit Order";
}

<h1>Edit Order</h1>

<partial name="_StatusMessage" model="TempData["Success"]" />

<form method="post">
    <input type="hidden" asp-for="OrderId" />
    <div asp-validation-summary="ModelOnly" class="text-danger"></div>

    <div class="mb-3">
        <label asp-for="Input.CustomerId" class="form-label"></label>
        <select asp-for="Input.CustomerId"
                asp-items="@(new SelectList(Model.Customers, "Id", "Name"))"
                class="form-select">
            <option value="">-- Select Customer --</option>
        </select>
        <span asp-validation-for="Input.CustomerId" class="text-danger"></span>
    </div>

    <div class="mb-3">
        <label asp-for="Input.Description" class="form-label"></label>
        <textarea asp-for="Input.Description" class="form-control" rows="3"></textarea>
        <span asp-validation-for="Input.Description" class="text-danger"></span>
    </div>

    <div class="mb-3">
        <label asp-for="Input.TotalAmount" class="form-label"></label>
        <input asp-for="Input.TotalAmount" class="form-control" />
        <span asp-validation-for="Input.TotalAmount" class="text-danger"></span>
    </div>

    <div class="mb-3">
        <label asp-for="Input.DueDate" class="form-label"></label>
        <input asp-for="Input.DueDate" class="form-control" />
        <span asp-validation-for="Input.DueDate" class="text-danger"></span>
    </div>

    <div class="d-flex gap-2">
        <button type="submit" asp-page-handler="Save" class="btn btn-primary">Save</button>
        <button type="submit" asp-page-handler="Cancel" class="btn btn-warning">Cancel Order</button>
        <button type="submit" asp-page-handler="Delete" class="btn btn-danger"
                onclick="return confirm('Are you sure?')">Delete</button>
        <a asp-page="./Index" class="btn btn-secondary">Back to List</a>
    </div>
</form>

@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

### View Components
```csharp
// ViewComponents/OrderSummaryViewComponent.cs
public class OrderSummaryViewComponent : ViewComponent
{
    private readonly IOrderService _orderService;

    public OrderSummaryViewComponent(IOrderService orderService)
        => _orderService = orderService;

    public async Task<IViewComponentResult> InvokeAsync(Guid customerId)
    {
        var summary = await _orderService.GetSummaryAsync(customerId);
        return View(summary);
    }
}

// Usage in Razor:
@await Component.InvokeAsync("OrderSummary", new { customerId = Model.CustomerId })
```

### Custom Tag Helpers
```csharp
[HtmlTargetElement("status-badge")]
public class StatusBadgeTagHelper : TagHelper
{
    [HtmlAttributeName("status")]
    public OrderStatus Status { get; set; }

    public override void Process(TagHelperContext context, TagHelperOutput output)
    {
        output.TagName = "span";
        output.Attributes.SetAttribute("class", $"badge bg-{GetStatusColor()}");
        output.Content.SetContent(Status.ToString());
    }

    private string GetStatusColor() => Status switch
    {
        OrderStatus.Pending => "warning",
        OrderStatus.Processing => "info",
        OrderStatus.Completed => "success",
        OrderStatus.Cancelled => "danger",
        _ => "secondary"
    };
}

// Usage: <status-badge status="Model.Order.Status" />
```

### Folder Structure
```
Pages/
├── _ViewImports.cshtml      # Global directives
├── _ViewStart.cshtml        # Layout assignment
├── Index.cshtml             # Home page
├── Error.cshtml
├── Shared/
│   ├── _Layout.cshtml
│   ├── _StatusMessage.cshtml
│   └── _ValidationScriptsPartial.cshtml
├── Orders/
│   ├── Index.cshtml         # List orders
│   ├── Index.cshtml.cs
│   ├── Create.cshtml        # Create order
│   ├── Create.cshtml.cs
│   ├── Edit.cshtml          # Edit order
│   ├── Edit.cshtml.cs
│   ├── Details.cshtml       # View order
│   ├── Details.cshtml.cs
│   └── _OrderForm.cshtml    # Shared partial
└── Account/
    ├── Login.cshtml
    └── Login.cshtml.cs
```

## Anti-Request Forgery
```csharp
// Automatically included with form tag helper
// For AJAX calls:
builder.Services.AddAntiforgery(options =>
{
    options.HeaderName = "X-CSRF-TOKEN";
});

// In JavaScript:
fetch('/api/orders', {
    method: 'POST',
    headers: {
        'X-CSRF-TOKEN': document.querySelector('input[name="__RequestVerificationToken"]').value
    }
});
```

## Checklist
- [ ] Page models follow single responsibility
- [ ] Input models separate from domain entities
- [ ] Validation attributes on all user inputs
- [ ] Anti-forgery tokens on all forms
- [ ] TempData for cross-request messages
- [ ] Proper use of asp-* tag helpers
- [ ] Partial views for reusable UI
- [ ] View components for complex reusable logic
- [ ] Proper error handling with try-catch or filters
- [ ] Authorization attributes on protected pages

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/abdullah-haytham03) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

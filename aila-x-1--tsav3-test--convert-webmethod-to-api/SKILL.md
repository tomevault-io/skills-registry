---
name: convert-webmethod-to-api
description: Convert ASP.NET 4.8 WebMethods from LargoV3 to .NET Core 8 API endpoints in AiLA_WebAPI. Use when asked to migrate, convert, or port WebMethods, ASPX page methods, or ASMX service methods to the new .NET Core API project. Creates DTOs, Repository interfaces and implementations, Controllers with proper ApiResponse wrappers, and registers dependencies. Use when this capability is needed.
metadata:
  author: aila-x-1
---

# Convert ASP.NET 4.8 WebMethod to .NET Core API

This skill guides the conversion of ASP.NET 4.8 WebMethods (from `LargoV3/`) to .NET Core 8 API endpoints (in `AiLA_WebAPI/`).

---

## Step 1: Analyze the Source WebMethod

Before converting, identify:
1. **Source file path** (e.g., `LargoV3/SomePage.aspx.cs`)
2. **Method name** and parameters
3. **Data access pattern** (raw SQL, Entity Framework, stored procedure)
4. **Return type** (anonymous object, string, specific type)
5. **Authorization requirements**

---

## Step 2: Create Required Files

For each conversion, create these files in `AiLA_WebAPI/AiLA_WebAPI/`:

### 2.1 Request DTO (if method has parameters)

**File**: `DTOs/{FeatureName}Dtos.cs` (or add to existing)

```csharp
namespace AiLA_WebAPI.DTOs
{
    public class {MethodName}RequestDto
    {
        public string Param1 { get; set; }
        public string Param2 { get; set; }
        // Map all WebMethod parameters as properties
    }
}
```

### 2.2 Response DTO (for complex return types)

```csharp
namespace AiLA_WebAPI.DTOs
{
    public class {MethodName}ResponseDto
    {
        // Map all properties from the anonymous object or return type
        public string Property1 { get; set; }
        public List<SubItemDto> Items { get; set; }
    }
}
```

### 2.3 Repository Interface

**File**: `Repositories/Interfaces/I{FeatureName}Repository.cs`

```csharp
namespace AiLA_WebAPI.Repositories.Interfaces
{
    public interface I{FeatureName}Repository
    {
        Task<{ResponseDto}> {MethodName}Async({RequestDto} request);
        // Or for simple returns:
        Task<string> {MethodName}Async(string param1, string param2);
    }
}
```

### 2.4 Repository Implementation

**File**: `Repositories/{FeatureName}Repository.cs`

```csharp
using AiLA_WebAPI.DTOs;
using AiLA_WebAPI.Repositories.Interfaces;
using Microsoft.EntityFrameworkCore;

namespace AiLA_WebAPI.Repositories
{
    public class {FeatureName}Repository : I{FeatureName}Repository
    {
        private readonly largomldbEntities _context;
        private readonly IConfiguration _configuration;

        public {FeatureName}Repository(largomldbEntities context, IConfiguration configuration)
        {
            _context = context;
            _configuration = configuration;
        }

        public async Task<{ResponseDto}> {MethodName}Async({RequestDto} request)
        {
            // Convert the data access logic here
            // Use async/await patterns
            // Return strongly-typed DTOs
        }
    }
}
```

### 2.5 Controller

**File**: `Controllers/{FeatureName}Controller.cs`

```csharp
using AiLA_WebAPI.DTOs;
using AiLA_WebAPI.Models;
using AiLA_WebAPI.Repositories.Interfaces;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;

namespace AiLA_WebAPI.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    [Authorize]  // Add if authentication required
    public class {FeatureName}Controller : BaseApiController
    {
        private readonly I{FeatureName}Repository _repository;
        private readonly ILogger<{FeatureName}Controller> _logger;

        public {FeatureName}Controller(
            I{FeatureName}Repository repository,
            ILogger<{FeatureName}Controller> logger)
        {
            _repository = repository;
            _logger = logger;
        }

        /// <summary>
        /// {Description of what the endpoint does}
        /// </summary>
        [HttpPost("{MethodName}")]
        [ProducesResponseType(typeof(ApiResponse<{ResponseDto}>), StatusCodes.Status200OK)]
        public async Task<IActionResult> {MethodName}([FromBody] {MethodName}RequestDto request)
        {
            // Validate required parameters (throw ValidationException for invalid input)
            if (string.IsNullOrWhiteSpace(request.RequiredParam))
            {
                throw new ValidationException("RequiredParam is required", nameof(request.RequiredParam));
            }

            var result = await _repository.{MethodName}Async(request);

            return Ok(ApiResponse<{ResponseDto}>.CreateSuccess(result, "Operation completed successfully"));
        }
    }
}
```

---

## Step 3: Register Repository in DI

**File**: `AiLA_WebAPI/ServiceCollectionExtensions.cs`

Add the repository registration:

```csharp
services.AddScoped<I{FeatureName}Repository, {FeatureName}Repository>();
```

---

## Conversion Patterns Reference

### Pattern 1: Anonymous Object Return → ApiResponse<T>

**OLD (ASP.NET 4.8)**:
```csharp
[WebMethod]
[ScriptMethod(ResponseFormat = ResponseFormat.Json)]
public object GetData(string param1)
{
    try
    {
        // ... data access ...
        return new { success = true, data = result };
    }
    catch (Exception ex)
    {
        return new { success = false, message = ex.Message };
    }
}
```

**NEW (.NET Core)**:

```csharp
[HttpPost("GetData")]
[ProducesResponseType(typeof(ApiResponse<DataDto>), StatusCodes.Status200OK)]
public async Task<IActionResult> GetData([FromBody] GetDataRequestDto request)
{
    var result = await _repository.GetDataAsync(request);
    return Ok(ApiResponse<DataDto>.CreateSuccess(result, "Data retrieved successfully"));
}
```

> **Note**: No try-catch needed - `GlobalExceptionHandlingMiddleware` handles all exceptions automatically.

### Pattern 2: Manual SQL → Repository with Stored Procedure

**OLD (ASP.NET 4.8)**:
```csharp
[WebMethod]
public static string GetDetails(string id)
{
    using (SqlConnection conn = new SqlConnection(connectionString))
    {
        conn.Open();
        using (SqlCommand cmd = new SqlCommand("SELECT * FROM Table WHERE Id = @Id", conn))
        {
            cmd.Parameters.AddWithValue("@Id", id);
            // ... execute and return
        }
    }
}
```

**NEW (.NET Core Repository)**:
```csharp
public async Task<DetailsDto> GetDetailsAsync(string id)
{
    using (var connection = _context.Database.GetDbConnection())
    {
        await connection.OpenAsync();
        using (var command = connection.CreateCommand())
        {
            command.CommandText = "SELECT * FROM Table WHERE Id = @Id";
            command.Parameters.Add(new SqlParameter("@Id", id));

            using (var reader = await command.ExecuteReaderAsync())
            {
                if (await reader.ReadAsync())
                {
                    return new DetailsDto
                    {
                        Property1 = reader["Column1"]?.ToString(),
                        // ... map other properties
                    };
                }
            }
        }
    }
    return null;
}
```

### Pattern 3: Entity Framework Inline → Repository with Async EF

**OLD (ASP.NET 4.8)**:
```csharp
[WebMethod]
public static string GetItems(string filter)
{
    using (largomldbEntities context = new largomldbEntities())
    {
        var items = context.SomeTable
            .Where(x => x.Name.Contains(filter))
            .ToList();
        return JsonConvert.SerializeObject(new { success = true, data = items });
    }
}
```

**NEW (.NET Core Repository)**:
```csharp
public async Task<List<ItemDto>> GetItemsAsync(string filter)
{
    return await _context.SomeTable
        .AsNoTracking()
        .Where(x => x.Name.Contains(filter))
        .Select(x => new ItemDto
        {
            Id = x.Id,
            Name = x.Name
        })
        .ToListAsync();
}
```

### Pattern 4: HttpContext.Current.User → Claims-based Identity

**OLD (ASP.NET 4.8)**:
```csharp
string username = HttpContext.Current.User.Identity.Name;
if (!AuthorizationHelper.IsAuthorizedUser()) return null;
```

**NEW (.NET Core Controller)**:
```csharp
// In controller (inherits BaseApiController)
var username = GetCurrentUser();  // Helper method from BaseApiController

// Or access claims directly:
var email = User.FindFirst(ClaimTypes.Email)?.Value;
```

### Pattern 5: LoadDropdowns/Page_Load → InitialLoad Endpoint

**IMPORTANT**: Many ASPX pages have `LoadDropdowns()` methods called in `Page_Load()` that populate dropdowns server-side. These MUST be converted to API endpoints.

**OLD (ASP.NET 4.8 - Page_Load with LoadDropdowns)**:
```csharp
protected void Page_Load(object sender, EventArgs e)
{
    LoadDropdowns();
}

private void LoadDropdowns()
{
    using (largomldbEntities context = new largomldbEntities())
    {
        var portfolioNames = context.portfolio_config
            .AsNoTracking()
            .Where(c => !c.portfolio_name.ToLower().Contains("template"))
            .Select(c => c.portfolio_name)
            .Distinct()
            .ToList();

        // Add metadata to dropdown items via data-* attributes
        var pmcodePortfolios = context.pmcode_portfolio_config
            .AsNoTracking()
            .Where(p => portfolioNames.Contains(p.portfolio_name))
            .ToDictionary(p => p.portfolio_name, p => p);

        ddlPortfolio.Items.Add(new ListItem("--Select--", "-1"));
        ddlPortfolio.Items.AddRange(
            portfolioNames.Select(name => {
                ListItem item = new ListItem(name, name);
                if (pmcodePortfolios.TryGetValue(name, out var config))
                {
                    item.Attributes.Add("data-pm-id", config.id.ToString());
                    item.Attributes.Add("data-pm-is-active", config.is_active.ToString().ToLower());
                    item.Attributes.Add("data-pm-aum", config.portfolio_aum?.ToString() ?? "");
                }
                return item;
            }).ToArray()
        );
    }
}
```

**NEW (.NET Core - InitialLoad/GetDropdowns endpoint)**:

**DTO** (`DTOs/{Feature}Dtos.cs`):
```csharp
public class PortfolioDropdownDto
{
    public string Name { get; set; }
    public long? ConfigId { get; set; }
    public bool? IsActive { get; set; }
    public decimal? Aum { get; set; }
}

public class {Feature}InitialLoadResponseDto
{
    public List<PortfolioDropdownDto> Portfolios { get; set; }
    // Add other dropdown lists as needed
}
```

**Repository**:
```csharp
public async Task<{Feature}InitialLoadResponseDto> GetInitialLoadAsync()
{
    var portfolioNames = await _context.portfolio_config
        .AsNoTracking()
        .Where(c => !c.portfolio_name.ToLower().Contains("template"))
        .Select(c => c.portfolio_name)
        .Distinct()
        .ToListAsync();

    var pmcodeConfigs = await _context.pmcode_portfolio_config
        .AsNoTracking()
        .Where(p => portfolioNames.Contains(p.portfolio_name))
        .ToDictionaryAsync(p => p.portfolio_name, p => p);

    return new {Feature}InitialLoadResponseDto
    {
        Portfolios = portfolioNames.Select(name => new PortfolioDropdownDto
        {
            Name = name,
            ConfigId = pmcodeConfigs.TryGetValue(name, out var config) ? config.id : null,
            IsActive = pmcodeConfigs.TryGetValue(name, out config) ? config.is_active : null,
            Aum = pmcodeConfigs.TryGetValue(name, out config) ? config.portfolio_aum : null
        }).ToList()
    };
}
```

**Controller**:
```csharp
/// <summary>
/// Get initial load data for dropdowns and form defaults
/// </summary>
[HttpGet("InitialLoad")]
[ProducesResponseType(typeof(ApiResponse<{Feature}InitialLoadResponseDto>), StatusCodes.Status200OK)]
public async Task<IActionResult> GetInitialLoad()
{
    var result = await _repository.GetInitialLoadAsync();
    return Ok(ApiResponse<{Feature}InitialLoadResponseDto>.CreateSuccess(result, "Initial data loaded successfully"));
}
```

> **Key Points**:
> - Use `[HttpGet]` since this is read-only data retrieval
> - Name the endpoint `InitialLoad`, `GetDropdowns`, or `GetPageData`
> - Include ALL dropdown data needed for the page in ONE response
> - Convert `data-*` attributes to DTO properties
> - Frontend calls this endpoint on page load to populate dropdowns

### Pattern 6: Stored Procedure with Multiple Result Sets

**OLD (ASP.NET 4.8)**:
```csharp
[WebMethod]
public static string GetReport(string date)
{
    using (SqlConnection conn = new SqlConnection(connStr))
    {
        SqlDataAdapter adapter = new SqlDataAdapter("usp_GetReport", conn);
        adapter.SelectCommand.CommandType = CommandType.StoredProcedure;
        adapter.SelectCommand.Parameters.AddWithValue("@Date", date);

        DataSet ds = new DataSet();
        adapter.Fill(ds);

        return JsonConvert.SerializeObject(new {
            table1 = ds.Tables[0],
            table2 = ds.Tables[1]
        });
    }
}
```

**NEW (.NET Core Repository)**:
```csharp
public async Task<ReportDto> GetReportAsync(string date)
{
    var result = new ReportDto();

    using (var connection = _context.Database.GetDbConnection())
    {
        await connection.OpenAsync();
        using (var command = connection.CreateCommand())
        {
            command.CommandText = "usp_GetReport";
            command.CommandType = CommandType.StoredProcedure;
            command.Parameters.Add(new SqlParameter("@Date", date));

            using (var adapter = new SqlDataAdapter((SqlCommand)command))
            {
                var dataSet = new DataSet();
                await Task.Run(() => adapter.Fill(dataSet));

                // Map first result set
                if (dataSet.Tables.Count > 0)
                {
                    result.Table1Data = dataSet.Tables[0].AsEnumerable()
                        .Select(row => new Table1Dto
                        {
                            Column1 = row["Column1"]?.ToString(),
                            Column2 = row["Column2"]?.ToString()
                        }).ToList();
                }

                // Map second result set
                if (dataSet.Tables.Count > 1)
                {
                    result.Table2Data = dataSet.Tables[1].AsEnumerable()
                        .Select(row => new Table2Dto
                        {
                            // ... map columns
                        }).ToList();
                }
            }
        }
    }
    return result;
}
```

---

## HTTP Method Selection Guide

| WebMethod Behavior | .NET Core Attribute |
|--------------------|---------------------|
| Retrieves data without side effects | `[HttpGet]` |
| Creates new resources | `[HttpPost]` |
| Updates existing resources | `[HttpPut]` or `[HttpPatch]` |
| Deletes resources | `[HttpDelete]` |
| Complex queries with body | `[HttpPost]` |
| Simple parameter queries | `[HttpGet]` with `[FromQuery]` |

**Note**: Most legacy WebMethods should use `[HttpPost]` since they typically accept complex parameters and may have side effects.

---

## Query Parameter vs Body Parameter

**Use `[FromQuery]`** for simple GET requests:

```csharp
[HttpGet("GetItem")]
[ProducesResponseType(typeof(ApiResponse<ItemDto>), StatusCodes.Status200OK)]
public async Task<IActionResult> GetItem(
    [FromQuery] string id,
    [FromQuery] string type = "default")
```

**Use `[FromBody]`** for complex data or POST requests:

```csharp
[HttpPost("SaveItem")]
[ProducesResponseType(typeof(ApiResponse<SaveResultDto>), StatusCodes.Status200OK)]
public async Task<IActionResult> SaveItem([FromBody] SaveItemRequestDto request)
```

---

## Checklist for Each Conversion

- [ ] Identify source WebMethod file and method
- [ ] **Check for `LoadDropdowns()` in `Page_Load()`** - if present, create `GET InitialLoad` endpoint
- [ ] Create Request DTO (if parameters exist)
- [ ] Create Response DTO (for return type)
- [ ] Create or update Repository Interface
- [ ] Implement Repository method with async pattern
- [ ] Create or update Controller
- [ ] Register repository in DI container
- [ ] Add appropriate authorization (`[Authorize]` or remove if public)
- [ ] Add XML documentation comments
- [ ] Add `[ProducesResponseType]` for 200 OK (for Swagger)
- [ ] Test endpoint with Swagger UI
- [ ] Update frontend JavaScript to call new API endpoint

---

## Existing Infrastructure Reference

### Global Exception Handling

The API uses `GlobalExceptionHandlingMiddleware` which catches all unhandled exceptions. **Do NOT wrap controller methods in try-catch blocks.** Instead:

- Throw `ValidationException` for input validation errors (400)
- Throw `NotFoundException` for missing resources (404)
- Throw `BadRequestException` for bad requests (400)
- Let other exceptions bubble up (automatically becomes 500)

### BaseApiController Helper Methods
- `GetCurrentUser()` - Returns authenticated user's email/name
- `ApiSuccess<T>(data, message)` - Create success response
- `ApiError<T>(message, statusCode, errors)` - Create error response

### ApiResponse<T> Factory Methods
- `ApiResponse<T>.CreateSuccess(data, message)` - Success wrapper
- `ApiResponse<T>.CreateError(message, statusCode, errorCode, errorMessage, field)` - Error wrapper

### DbContext Available
- `largomldbEntities` - Main database context (registered in DI)
- `AilaIndicesdbEntities` - Indices database context

### Database Schema Prefixes
- **Tracker tables**: Always use `Tracker.` schema prefix (e.g., `Tracker.tracker_portfolio_config`, `Tracker.tracker_plus_benchmark_config`)
- **Default schema (dbo)**: No prefix needed for most tables

---

## Example: Complete Conversion

### Source WebMethod (LargoV3/SomePage.aspx.cs)
```csharp
[WebMethod]
public static string GetPortfolioAllocation(string portfolioName)
{
    try
    {
        if (!AuthorizationHelper.IsAuthorizedUser()) return null;

        using (largomldbEntities context = new largomldbEntities())
        {
            var allocations = context.portfolio_allocations
                .Where(a => a.portfolio_name == portfolioName)
                .Select(a => new { a.asset_name, a.weight })
                .ToList();

            return JsonConvert.SerializeObject(new { success = true, data = allocations });
        }
    }
    catch (Exception ex)
    {
        return JsonConvert.SerializeObject(new { success = false, message = ex.Message });
    }
}
```

### Converted to .NET Core

**DTO** (`DTOs/PortfolioAllocationDtos.cs`):
```csharp
namespace AiLA_WebAPI.DTOs
{
    public class GetPortfolioAllocationRequestDto
    {
        public string PortfolioName { get; set; }
    }

    public class PortfolioAllocationDto
    {
        public string AssetName { get; set; }
        public decimal Weight { get; set; }
    }
}
```

**Repository Interface** (`Repositories/Interfaces/IPortfolioRepository.cs`):
```csharp
public interface IPortfolioRepository
{
    Task<List<PortfolioAllocationDto>> GetPortfolioAllocationAsync(string portfolioName);
}
```

**Repository** (`Repositories/PortfolioRepository.cs`):
```csharp
public class PortfolioRepository : IPortfolioRepository
{
    private readonly largomldbEntities _context;

    public PortfolioRepository(largomldbEntities context)
    {
        _context = context;
    }

    public async Task<List<PortfolioAllocationDto>> GetPortfolioAllocationAsync(string portfolioName)
    {
        return await _context.portfolio_allocations
            .AsNoTracking()
            .Where(a => a.portfolio_name == portfolioName)
            .Select(a => new PortfolioAllocationDto
            {
                AssetName = a.asset_name,
                Weight = a.weight
            })
            .ToListAsync();
    }
}
```

**Controller** (`Controllers/PortfolioController.cs`):
```csharp
[Route("api/[controller]")]
[ApiController]
[Authorize]
public class PortfolioController : BaseApiController
{
    private readonly IPortfolioRepository _repository;
    private readonly ILogger<PortfolioController> _logger;

    public PortfolioController(IPortfolioRepository repository, ILogger<PortfolioController> logger)
    {
        _repository = repository;
        _logger = logger;
    }

    /// <summary>
    /// Get portfolio allocation by portfolio name
    /// </summary>
    [HttpPost("GetPortfolioAllocation")]
    [ProducesResponseType(typeof(ApiResponse<List<PortfolioAllocationDto>>), StatusCodes.Status200OK)]
    public async Task<IActionResult> GetPortfolioAllocation([FromBody] GetPortfolioAllocationRequestDto request)
    {
        if (string.IsNullOrWhiteSpace(request.PortfolioName))
        {
            throw new ValidationException("PortfolioName is required", nameof(request.PortfolioName));
        }

        var result = await _repository.GetPortfolioAllocationAsync(request.PortfolioName);

        return Ok(ApiResponse<List<PortfolioAllocationDto>>.CreateSuccess(
            result, "Portfolio allocation retrieved successfully"));
    }
}
```

**Register in DI** (`ServiceCollectionExtensions.cs`):
```csharp
services.AddScoped<IPortfolioRepository, PortfolioRepository>();
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aila-x-1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

---
name: auth0-integration
description: Auth0 OAuth2/OpenID Connect integration for Blazor + ASP.NET Core with JWT validation, role-based authorization, and secure token management Use when this capability is needed.
metadata:
  author: mpaulosky
---

# Auth0 Integration Skill — IssueTracker Backend & Frontend

## Overview

**What Auth0 Provides:**
- **OAuth 2.0 / OpenID Connect** identity provider with zero trust defaults
- **JWT token generation** for stateless authentication across APIs
- **Multi-factor authentication (MFA)** and passwordless login options
- **Role-based access control (RBAC)** via custom claims
- **Session management** and token refresh flows
- **Audit logging** for compliance and security tracking

**Why We Choose Auth0:**
1. **Reduced Auth Complexity** — No password hashing, salt management, or session state
2. **Compliance Ready** — Built-in MFA, audit logs, and OAuth compliance
3. **Minimal API Surface** — Blazor + ASP.NET Core middleware handle token flow natively
4. **Scalability** — Auth0 handles millions of tokens; we only validate them
5. **Developer Experience** — Simple configuration in Aspire (ServiceDefaults)

---

## Frontend Integration (Stansfield)

### 1. Login/Logout Flows in Blazor

**Login Page Example:**
```razor
@* src/Web/Pages/LoginPage.razor *@
@page "/login"
@using Auth0.Blazor
@inject NavigationManager NavManager
@inject Auth0Client Auth0

<PageTitle>Login — IssueTracker</PageTitle>

<div class="flex items-center justify-center min-h-screen bg-gradient-to-br from-blue-600 to-blue-800">
	<div class="bg-white rounded-lg shadow-lg p-8 w-96">
		<h1 class="text-2xl font-bold text-center mb-6">IssueTracker</h1>
		
		@if (IsAuthenticating)
		{
			<p class="text-center text-gray-600">Redirecting to login...</p>
		}
		else
		{
			<button 
				@onclick="LoginAsync" 
				class="w-full bg-blue-600 hover:bg-blue-700 text-white font-semibold py-3 px-4 rounded-lg transition">
				Login with Auth0
			</button>
		}
	</div>
</div>

@code {
	private bool IsAuthenticating = false;

	private async Task LoginAsync()
	{
		IsAuthenticating = true;
		await Auth0.LoginAsync(new Auth0ClientOptions 
		{ 
			RedirectUri = new Uri(NavManager.BaseUri).ToString() 
		});
	}
}
```

**Logout Button Component:**
```razor
@* src/Web/Components/LogoutButtonComponent.razor *@
@using Auth0.Blazor
@inject Auth0Client Auth0
@inject NavigationManager NavManager

<button 
	@onclick="LogoutAsync" 
	class="px-4 py-2 bg-red-600 hover:bg-red-700 text-white rounded-lg font-semibold transition">
	Logout
</button>

@code {
	private async Task LogoutAsync()
	{
		await Auth0.LogoutAsync(new Auth0ClientOptions 
		{ 
			ReturnTo = new Uri(NavManager.BaseUri).ToString() 
		});
	}
}
```

### 2. Extracting Claims and Tokens

**Claims Access in Components:**
```csharp
@* src/Web/Components/UserProfileComponent.razor.cs *@
using Auth0.Blazor;
using System.Security.Claims;

namespace IssueTracker.Web.Components;

public partial class UserProfileComponent
{
	[Inject] private AuthenticationStateProvider AuthStateProvider { get; set; } = null!;
	
	private string? UserEmail { get; set; }
	private string? UserName { get; set; }
	private string[]? UserRoles { get; set; }

	protected override async Task OnInitializedAsync()
	{
		var authState = await AuthStateProvider.GetAuthenticationStateAsync();
		var user = authState.User;

		if (user.Identity?.IsAuthenticated ?? false)
		{
			UserEmail = user.FindFirst(ClaimTypes.Email)?.Value;
			UserName = user.FindFirst(ClaimTypes.Name)?.Value;
			UserRoles = user.FindAll("roles")
				.Select(c => c.Value)
				.ToArray();
		}
	}
}
```

**Accessing the ID Token:**
```csharp
public partial class DashboardPage
{
	[Inject] private Auth0Client Auth0Client { get; set; } = null!;
	
	private string? IdToken { get; set; }

	protected override async Task OnInitializedAsync()
	{
		var user = await Auth0Client.GetUserAsync();
		if (user is not null)
		{
			IdToken = await Auth0Client.GetTokenAsync();
		}
	}
}
```

### 3. Protecting Blazor Pages with [Authorize]

**Using Authorization Directive:**
```razor
@* src/Web/Pages/IssuePage.razor *@
@page "/issues"
@attribute [Authorize]
@using Auth0.Blazor

<PageTitle>Issues — IssueTracker</PageTitle>

<h1>My Issues</h1>
@* Page content only visible to authenticated users *@
```

**Protecting Routes with Role Requirements:**
```razor
@* src/Web/Pages/AdminPage.razor *@
@page "/admin"
@attribute [Authorize(Roles = "admin")]

<PageTitle>Admin Panel — IssueTracker</PageTitle>

<h1>Administration</h1>
@* Only users with "admin" role can see this *@
```

**Custom Authorize Component (Optional):**
```razor
@* src/Web/Components/AuthorizedOnlyComponent.razor *@
@using Auth0.Blazor

<CascadingAuthenticationState>
	<AuthorizeView>
		<Authorized>
			@ChildContent
		</Authorized>
		<NotAuthorized>
			<p class="text-red-600 font-semibold">Access Denied. Please log in.</p>
		</NotAuthorized>
	</AuthorizeView>
</CascadingAuthenticationState>

@code {
	[Parameter]
	public RenderFragment? ChildContent { get; set; }
}
```

---

## Backend Integration (Wolinski)

### 1. Configuring ASP.NET Core to Validate Auth0 Tokens

**JWT Bearer Authentication Setup in Program.cs:**
```csharp
// src/IssueTracker.API/Program.cs
using IssueTracker.ServiceDefaults;
using Microsoft.AspNetCore.Authentication.JwtBearer;

var builder = WebApplicationBuilder.CreateBuilder(args);

// Auth0 Configuration
var auth0Domain = builder.Configuration["Auth0:Domain"] ?? throw new InvalidOperationException("Auth0:Domain not configured");
var auth0Audience = builder.Configuration["Auth0:Audience"] ?? throw new InvalidOperationException("Auth0:Audience not configured");

// Add JWT Bearer authentication
builder.Services
	.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
	.AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options =>
	{
		options.Authority = $"https://{auth0Domain}/";
		options.Audience = auth0Audience;
		
		// Token validation settings
		options.TokenValidationParameters = new()
		{
			ValidateIssuer = true,
			ValidIssuer = $"https://{auth0Domain}/",
			ValidateAudience = true,
			ValidAudience = auth0Audience,
			ValidateLifetime = true,
			ClockSkew = TimeSpan.FromSeconds(60) // Allow 60 seconds clock skew
		};

		// Handle token validation failures
		options.Events = new JwtBearerEvents
		{
			OnAuthenticationFailed = context =>
			{
				if (context.Exception is SecurityTokenExpiredException)
				{
					context.Response.StatusCode = 401;
					context.Response.ContentType = "application/json";
					return context.Response.WriteAsJsonAsync(new { error = "Token expired" });
				}

				return Task.CompletedTask;
			},
			OnTokenValidated = context =>
			{
				var claims = context.Principal?.Claims ?? [];
				// Optional: Log token validation for audit
				return Task.CompletedTask;
			}
		};
	});

// Add authorization services
builder.Services.AddAuthorization(options =>
{
	// Define policies for common authorization scenarios
	options.AddPolicy("admin", policy => policy.RequireRole("admin"));
	options.AddPolicy("moderator", policy => policy.RequireRole("moderator", "admin"));
});

// Add service defaults (logging, health checks, tracing)
builder.AddServiceDefaults();

var app = builder.Build();

// Use authentication and authorization middleware (ORDER MATTERS)
app.UseAuthentication();
app.UseAuthorization();

// Map API endpoints
var api = app.MapGroup("/api/v1").RequireAuthorization();

api.MapGet("/issues", GetIssuesAsync)
	.WithName("GetIssues")
	.WithOpenApi()
	.Produces<IEnumerable<IssueDto>>(StatusCodes.Status200OK)
	.Produces(StatusCodes.Status401Unauthorized);

api.MapPost("/issues", CreateIssueAsync)
	.WithName("CreateIssue")
	.WithOpenApi()
	.Produces<IssueDto>(StatusCodes.Status201Created)
	.Produces(StatusCodes.Status401Unauthorized);

app.Run();

async Task<IResult> GetIssuesAsync(
	IIssueService issueService,
	ClaimsPrincipal user)
{
	var userId = user.FindFirst(ClaimTypes.NameIdentifier)?.Value;
	if (userId is null)
		return Results.Unauthorized();

	var issues = await issueService.GetUserIssuesAsync(userId);
	return Results.Ok(issues);
}

async Task<IResult> CreateIssueAsync(
	CreateIssueRequest request,
	IIssueService issueService,
	IValidator<CreateIssueRequest> validator,
	ClaimsPrincipal user)
{
	var validationResult = await validator.ValidateAsync(request);
	if (!validationResult.IsValid)
		return Results.BadRequest(validationResult.Errors);

	var userId = user.FindFirst(ClaimTypes.NameIdentifier)?.Value;
	if (userId is null)
		return Results.Unauthorized();

	var issue = await issueService.CreateIssueAsync(request, userId);
	return Results.Created($"/api/v1/issues/{issue.Id}", issue);
}
```

### 2. Setting Up JWT Bearer Authentication in Program.cs

**ServiceDefaults Integration:**
```csharp
// src/IssueTracker.ServiceDefaults/Extensions.cs
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.Extensions.Diagnostics.HealthChecks;

namespace IssueTracker.ServiceDefaults;

public static class ServiceDefaultsExtensions
{
	public static IHostBuilder AddServiceDefaults(this IHostBuilder builder)
	{
		builder.ConfigureServices(services =>
		{
			// Structured logging
			services.AddLogging(logging =>
			{
				logging.AddConsole();
				logging.AddDebug();
			});

			// Health checks
			services.AddHealthChecks()
				.AddCheck("jwt", () => HealthCheckResult.Healthy("JWT validation healthy"));

			// OpenTelemetry
			services.AddOpenTelemetry()
				.WithTracing(tracing => tracing
					.AddAspNetCoreInstrumentation()
					.AddHttpClientInstrumentation());
		});

		return builder;
	}

	public static WebApplication UseServiceDefaults(this WebApplication app)
	{
		app.MapHealthChecks("/health");
		app.UseAuthentication();
		app.UseAuthorization();
		return app;
	}
}
```

### 3. Extracting User Identity in API Endpoints

**Accessing Claims in Endpoints:**
```csharp
// src/IssueTracker.API/Endpoints/IssueEndpoints.cs
using System.Security.Claims;

namespace IssueTracker.API.Endpoints;

public static class IssueEndpoints
{
	public static void MapIssueEndpoints(this WebApplication app)
	{
		var group = app.MapGroup("/api/v1/issues")
			.RequireAuthorization()
			.WithTags("Issues");

		group.MapGet("/{id}", GetIssueByIdAsync);
		group.MapPost("", CreateIssueAsync);
		group.MapPut("/{id}", UpdateIssueAsync);
		group.MapDelete("/{id}", DeleteIssueAsync);
	}

	private static async Task<IResult> GetIssueByIdAsync(
		string id,
		IIssueService issueService,
		ClaimsPrincipal user)
	{
		var userId = user.FindFirst(ClaimTypes.NameIdentifier)?.Value
			?? throw new UnauthorizedAccessException("User ID not found in claims");

		var issue = await issueService.GetIssueAsync(id, userId);
		return issue is null 
			? Results.NotFound() 
			: Results.Ok(issue);
	}

	private static async Task<IResult> CreateIssueAsync(
		CreateIssueRequest request,
		IIssueService issueService,
		ClaimsPrincipal user)
	{
		var userId = user.FindFirst(ClaimTypes.NameIdentifier)?.Value
			?? throw new UnauthorizedAccessException("User ID not found in claims");
		
		var userEmail = user.FindFirst(ClaimTypes.Email)?.Value ?? "unknown@example.com";

		var issue = await issueService.CreateIssueAsync(request with 
		{ 
			CreatedBy = userId, 
			CreatedByEmail = userEmail 
		});

		return Results.Created($"/api/v1/issues/{issue.Id}", issue);
	}

	private static async Task<IResult> UpdateIssueAsync(
		string id,
		UpdateIssueRequest request,
		IIssueService issueService,
		ClaimsPrincipal user)
	{
		var userId = user.FindFirst(ClaimTypes.NameIdentifier)?.Value
			?? throw new UnauthorizedAccessException("User ID not found in claims");

		var issue = await issueService.UpdateIssueAsync(id, request, userId);
		return issue is null 
			? Results.Forbid() 
			: Results.Ok(issue);
	}

	private static async Task<IResult> DeleteIssueAsync(
		string id,
		IIssueService issueService,
		ClaimsPrincipal user)
	{
		var userId = user.FindFirst(ClaimTypes.NameIdentifier)?.Value
			?? throw new UnauthorizedAccessException("User ID not found in claims");

		var success = await issueService.DeleteIssueAsync(id, userId);
		return success 
			? Results.NoContent() 
			: Results.Forbid();
	}
}

public record CreateIssueRequest(string Title, string? Description);
public record UpdateIssueRequest(string Title, string? Description, string Status);
```

### 4. Role-Based Authorization

**Authorization Policy Examples:**
```csharp
// src/IssueTracker.API/Endpoints/AdminEndpoints.cs
using System.Security.Claims;

namespace IssueTracker.API.Endpoints;

public static class AdminEndpoints
{
	public static void MapAdminEndpoints(this WebApplication app)
	{
		var group = app.MapGroup("/api/v1/admin")
			.RequireAuthorization("admin")
			.WithTags("Admin");

		group.MapGet("/users", GetAllUsersAsync);
		group.MapPost("/users/{userId}/roles", AssignRoleAsync);
		group.MapDelete("/users/{userId}/roles/{role}", RemoveRoleAsync);
	}

	private static async Task<IResult> GetAllUsersAsync(
		IUserService userService,
		ClaimsPrincipal user)
	{
		var adminId = user.FindFirst(ClaimTypes.NameIdentifier)?.Value;
		var users = await userService.GetAllUsersAsync();
		return Results.Ok(users);
	}

	private static async Task<IResult> AssignRoleAsync(
		string userId,
		string role,
		IUserService userService,
		ClaimsPrincipal user)
	{
		if (!user.IsInRole("admin"))
			return Results.Forbid();

		var success = await userService.AssignRoleAsync(userId, role);
		return success 
			? Results.Ok() 
			: Results.BadRequest("Failed to assign role");
	}

	private static async Task<IResult> RemoveRoleAsync(
		string userId,
		string role,
		IUserService userService)
	{
		var success = await userService.RemoveRoleAsync(userId, role);
		return success 
			? Results.NoContent() 
			: Results.BadRequest("Failed to remove role");
	}
}
```

**Custom Authorization Attribute (Optional):**
```csharp
// src/IssueTracker.API/Authorization/IssueOwnerHandler.cs
using System.Security.Claims;
using Microsoft.AspNetCore.Authorization;

namespace IssueTracker.API.Authorization;

public class IssueOwnerRequirement : IAuthorizationRequirement { }

public class IssueOwnerHandler : AuthorizationHandler<IssueOwnerRequirement>
{
	private readonly IIssueService _issueService;

	public IssueOwnerHandler(IIssueService issueService)
	{
		_issueService = issueService;
	}

	protected override async Task HandleRequirementAsync(
		AuthorizationHandlerContext context,
		IssueOwnerRequirement requirement)
	{
		var issueId = context.Resource as string;
		var userId = context.User.FindFirst(ClaimTypes.NameIdentifier)?.Value;

		if (string.IsNullOrEmpty(issueId) || string.IsNullOrEmpty(userId))
		{
			context.Fail();
			return;
		}

		var issue = await _issueService.GetIssueAsync(issueId, userId);
		if (issue is not null)
		{
			context.Succeed(requirement);
		}
	}
}
```

---

## Configuration

### 1. Required Auth0 Settings

**Auth0 Dashboard Setup:**
1. Create a new Application in Auth0 Dashboard
2. Choose **Regular Web Application** (for Blazor Server)
3. Note your:
   - **Domain**: `your-tenant.auth0.com`
   - **Client ID**: `abc123xyz...`
   - **Client Secret**: (for backend-to-backend communication)
4. Set Allowed Callback URLs: `https://localhost:5001/callback`
5. Set Allowed Logout URLs: `https://localhost:5001`
6. In **APIs**, create a new API:
   - **Name**: `IssueTracker API`
   - **Identifier**: `https://api.issuetracker.local`
   - This identifier is your **Audience**

**Auth0 Roles Setup:**
```
1. Go to Auth0 Dashboard → User Management → Roles
2. Create roles:
   - admin: Full system access
   - moderator: Can review and update issues
   - viewer: Read-only access
3. Create permissions (optional):
   - issues:create
   - issues:read
   - issues:update
   - issues:delete
   - users:manage
```

### 2. User Secrets Setup for Local Dev

**Initialize User Secrets:**
```powershell
# In src/IssueTracker.API folder
dotnet user-secrets init
dotnet user-secrets set "Auth0:Domain" "your-tenant.auth0.com"
dotnet user-secrets set "Auth0:Audience" "https://api.issuetracker.local"
dotnet user-secrets set "Auth0:ClientId" "your-client-id"
dotnet user-secrets set "Auth0:ClientSecret" "your-client-secret"
```

**User Secrets File (Linux/Mac):**
```
~/.microsoft/usersecrets/IssueTracker.API-id/secrets.json
```

**User Secrets File (Windows):**
```
%APPDATA%\Microsoft\UserSecrets\IssueTracker.API-id\secrets.json
```

**appsettings.json (Shared Values):**
```json
{
	"Auth0": {
		"Domain": "your-tenant.auth0.com",
		"Audience": "https://api.issuetracker.local"
	},
	"Logging": {
		"LogLevel": {
			"Default": "Information",
			"Microsoft.AspNetCore": "Warning"
		}
	}
}
```

### 3. Environment Variables for Deployment

**Azure App Service / Docker Environment Variables:**
```bash
# .env (for docker-compose.yml)
AUTH0_DOMAIN=your-tenant.auth0.com
AUTH0_AUDIENCE=https://api.issuetracker.local
AUTH0_CLIENT_ID=your-client-id
AUTH0_CLIENT_SECRET=your-client-secret

# Blazor Client Configuration
BLAZOR_AUTH0_DOMAIN=your-tenant.auth0.com
BLAZOR_AUTH0_CLIENT_ID=your-spa-client-id
```

**GitHub Actions Secrets:**
```yaml
# .github/workflows/deploy.yml
env:
	AUTH0_DOMAIN: ${{ secrets.AUTH0_DOMAIN }}
	AUTH0_AUDIENCE: ${{ secrets.AUTH0_AUDIENCE }}
	AUTH0_CLIENT_ID: ${{ secrets.AUTH0_CLIENT_ID }}
	AUTH0_CLIENT_SECRET: ${{ secrets.AUTH0_CLIENT_SECRET }}
```

**Aspire AppHost Configuration:**
```csharp
// src/IssueTracker.AppHost/Program.cs
var auth0Domain = builder.AddParameter("auth0-domain", secret: true);
var auth0Audience = builder.AddParameter("auth0-audience", secret: true);
var auth0ClientId = builder.AddParameter("auth0-client-id", secret: true);

var api = builder.AddProject<Projects.IssueTracker_API>("api")
	.WithEnvironment("Auth0__Domain", auth0Domain)
	.WithEnvironment("Auth0__Audience", auth0Audience)
	.WithEnvironment("Auth0__ClientId", auth0ClientId);
```

---

## Testing

### 1. Mocking Auth0 for Integration Tests

**Test Fixture with Mock Auth0 Token:**
```csharp
// tests/IssueTracker.Tests.Integration/Fixtures/Auth0MockFixture.cs
using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;
using Microsoft.IdentityModel.Tokens;
using Xunit;

namespace IssueTracker.Tests.Integration.Fixtures;

public class Auth0MockFixture : IAsyncLifetime
{
	private const string TestSecurityKey = "a-very-long-secret-key-for-jwt-signing-that-is-at-least-32-characters-long";
	private readonly SymmetricSecurityKey _signingKey = new(Encoding.UTF8.GetBytes(TestSecurityKey));

	public string TestUserId { get; } = "auth0|test-user-123";
	public string TestUserEmail { get; } = "testuser@example.com";
	public string TestUserName { get; } = "Test User";

	public async Task InitializeAsync() => await Task.CompletedTask;

	public async Task DisposeAsync() => await Task.CompletedTask;

	/// <summary>
	/// Generates a mock JWT token for testing.
	/// </summary>
	public string GenerateMockToken(
		string userId = "",
		string[]? roles = null,
		DateTime? expiresAt = null)
	{
		var claims = new List<Claim>
		{
			new(ClaimTypes.NameIdentifier, userId ?? TestUserId),
			new(ClaimTypes.Email, TestUserEmail),
			new(ClaimTypes.Name, TestUserName),
			new("sub", userId ?? TestUserId),
		};

		if (roles is not null)
		{
			foreach (var role in roles)
			{
				claims.Add(new Claim("roles", role));
			}
		}

		var credentials = new SigningCredentials(_signingKey, SecurityAlgorithms.HmacSha256);
		var token = new JwtSecurityToken(
			issuer: "https://test-tenant.auth0.com/",
			audience: "https://api.issuetracker.local",
			claims: claims,
			expires: expiresAt ?? DateTime.UtcNow.AddHours(1),
			signingCredentials: credentials);

		return new JwtSecurityTokenHandler().WriteToken(token);
	}
}
```

### 2. Test Fixtures for Authenticated Requests

**WebApplicationFactory with Auth0 Mock:**
```csharp
// tests/IssueTracker.Tests.Integration/Fixtures/ApiApplicationFactory.cs
using Microsoft.AspNetCore.Mvc.Testing;
using Microsoft.Extensions.DependencyInjection;
using Xunit;

namespace IssueTracker.Tests.Integration.Fixtures;

public class ApiApplicationFactory : WebApplicationFactory<Program>, IAsyncLifetime
{
	private readonly Auth0MockFixture _auth0Mock = new();
	private readonly MongoDbFixture _mongoDb = new();

	public Auth0MockFixture Auth0Mock => _auth0Mock;
	public MongoDbFixture MongoDb => _mongoDb;

	public async Task InitializeAsync()
	{
		await _auth0Mock.InitializeAsync();
		await _mongoDb.InitializeAsync();
	}

	public async Task DisposeAsync()
	{
		await _auth0Mock.DisposeAsync();
		await _mongoDb.DisposeAsync();
	}

	protected override void ConfigureWebHost(IWebHostBuilder builder)
	{
		builder.ConfigureServices(services =>
		{
			// Use test MongoDB
			services.Configure<MongoDbSettings>(options =>
			{
				options.ConnectionString = _mongoDb.ConnectionString;
				options.DatabaseName = _mongoDb.DatabaseName;
			});

			// Add mock Auth0 (optional: replace with mock provider)
		});
	}

	public HttpClient CreateAuthenticatedClient(string[]? roles = null)
	{
		var client = CreateClient();
		var token = _auth0Mock.GenerateMockToken(roles: roles);
		client.DefaultRequestHeaders.Authorization = new("Bearer", token);
		return client;
	}
}
```

**Integration Test Example:**
```csharp
// tests/IssueTracker.Tests.Integration/Tests/IssueEndpointsTests.cs
using FluentAssertions;
using Xunit;

namespace IssueTracker.Tests.Integration.Tests;

public class IssueEndpointsTests : IAsyncLifetime
{
	private readonly ApiApplicationFactory _factory = new();
	private HttpClient _client = null!;

	public async Task InitializeAsync()
	{
		await _factory.InitializeAsync();
		_client = _factory.CreateAuthenticatedClient();
	}

	public async Task DisposeAsync()
	{
		_client.Dispose();
		await _factory.DisposeAsync();
	}

	[Fact]
	public async Task CreateIssue_WithValidRequest_ReturnsCreatedStatus()
	{
		// Arrange
		var request = new CreateIssueRequest("Test Issue", "This is a test");
		var content = JsonContent.Create(request);

		// Act
		var response = await _client.PostAsync("/api/v1/issues", content);

		// Assert
		response.StatusCode.Should().Be(StatusCodes.Status201Created);
		var issue = await response.Content.ReadAsAsync<IssueDto>();
		issue.Title.Should().Be("Test Issue");
	}

	[Fact]
	public async Task GetIssues_WithoutToken_ReturnsUnauthorized()
	{
		// Arrange
		var unauthorizedClient = _factory.CreateClient();

		// Act
		var response = await unauthorizedClient.GetAsync("/api/v1/issues");

		// Assert
		response.StatusCode.Should().Be(StatusCodes.Status401Unauthorized);
	}

	[Fact]
	public async Task DeleteIssue_WithAdminRole_ReturnsSuccess()
	{
		// Arrange
		var adminClient = _factory.CreateAuthenticatedClient(roles: new[] { "admin" });

		// Act
		var response = await adminClient.DeleteAsync("/api/v1/issues/issue-id-123");

		// Assert
		response.StatusCode.Should().Be(StatusCodes.Status204NoContent);
	}

	[Fact]
	public async Task DeleteIssue_WithViewerRole_ReturnsForbidden()
	{
		// Arrange
		var viewerClient = _factory.CreateAuthenticatedClient(roles: new[] { "viewer" });

		// Act
		var response = await viewerClient.DeleteAsync("/api/v1/issues/issue-id-123");

		// Assert
		response.StatusCode.Should().Be(StatusCodes.Status403Forbidden);
	}
}
```

**Unit Test with NSubstitute:**
```csharp
// tests/IssueTracker.Tests.Unit/Services/IssueServiceTests.cs
using NSubstitute;
using FluentAssertions;
using System.Security.Claims;
using Xunit;

namespace IssueTracker.Tests.Unit.Services;

public class IssueServiceTests
{
	private readonly IIssueRepository _repository = Substitute.For<IIssueRepository>();
	private readonly IssueService _service;

	public IssueServiceTests()
	{
		_service = new IssueService(_repository);
	}

	[Fact]
	public async Task CreateIssueAsync_WithValidInput_ReturnsIssueDtoWithUserIdSet()
	{
		// Arrange
		var request = new CreateIssueRequest("New Issue", "Description");
		var userId = "auth0|user-123";

		// Act
		var result = await _service.CreateIssueAsync(request, userId);

		// Assert
		result.CreatedBy.Should().Be(userId);
		result.Title.Should().Be("New Issue");
		await _repository.Received(1).AddAsync(Arg.Any<Issue>());
	}

	[Fact]
	public async Task DeleteIssueAsync_WithWrongUser_ReturnsFalse()
	{
		// Arrange
		var issueId = "issue-123";
		var userId = "auth0|user-456";
		var issue = new Issue { Id = issueId, CreatedBy = "auth0|user-789" };
		_repository.GetByIdAsync(issueId).Returns(issue);

		// Act
		var result = await _service.DeleteIssueAsync(issueId, userId);

		// Assert
		result.Should().BeFalse();
		await _repository.DidNotReceive().DeleteAsync(issueId);
	}
}
```

---

## Quick Start Checklist

- [ ] Configure Auth0 Application (callback URLs, logout URLs, API audience)
- [ ] Create Auth0 API with identifier: `https://api.issuetracker.local`
- [ ] Create roles in Auth0 (admin, moderator, viewer)
- [ ] Install NuGet packages:
  - `Microsoft.AspNetCore.Authentication.JwtBearer`
  - `Auth0.Blazor` (frontend)
  - `System.IdentityModel.Tokens.Jwt` (for testing)
- [ ] Add `Auth0:Domain` and `Auth0:Audience` to `appsettings.json`
- [ ] Set user secrets locally: `dotnet user-secrets set "Auth0:Domain" "..."`
- [ ] Configure JWT bearer in `Program.cs`
- [ ] Add `[Authorize]` attribute to protected endpoints
- [ ] Extract `ClaimsPrincipal` in endpoint handlers
- [ ] Create test fixtures (Auth0MockFixture, ApiApplicationFactory)
- [ ] Write integration tests with authenticated clients

---

## References

- **Auth0 Docs**: https://auth0.com/docs
- **ASP.NET Core Authentication**: https://learn.microsoft.com/en-us/aspnet/core/security/authentication
- **JWT Bearer**: https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.authentication.jwtbearer
- **Blazor Authorization**: https://learn.microsoft.com/en-us/aspnet/core/blazor/security

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mpaulosky) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

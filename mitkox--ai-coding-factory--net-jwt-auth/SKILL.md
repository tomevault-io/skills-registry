---
name: net-jwt-auth
description: Implement JWT authentication and authorization for ASP.NET Core Use when this capability is needed.
metadata:
  author: mitkox
---

## What I Do

I implement complete JWT authentication:
- JWT token generation
- Token validation
- Refresh token support
- Role-based authorization
- Password hashing
- User claims

## When to Use Me

Use this skill when:
- Adding authentication to API
- Implementing JWT tokens
- Setting up authorization
- Securing API endpoints

## Authentication Structure

```
src/{ProjectName}.Infrastructure/Security/
├── Jwt/
│   ├── IJwtTokenGenerator.cs
│   ├── JwtTokenGenerator.cs
│   ├── IJwtTokenValidator.cs
│   └── JwtTokenValidator.cs
├── Password/
│   ├── IPasswordHasher.cs
│   └── PasswordHasher.cs
├── Claims/
│   └── ClaimConstants.cs
└── Extensions/
    └── AuthenticationExtensions.cs

src/{ProjectName}.Application/Authentication/
├── Commands/
│   ├── RegisterCommand.cs
│   ├── RegisterCommandHandler.cs
│   ├── LoginCommand.cs
│   └── LoginCommandHandler.cs
├── DTOs/
│   ├── RegisterRequest.cs
│   ├── LoginRequest.cs
│   └── AuthResponse.cs
└── Services/
    └── IAuthService.cs
```

## JWT Implementation

### Token Generation
```csharp
public class JwtTokenGenerator : IJwtTokenGenerator
{
    private readonly IOptions<JwtSettings> _jwtSettings;

    public JwtTokenGenerator(IOptions<JwtSettings> jwtSettings)
    {
        _jwtSettings = jwtSettings;
    }

    public string GenerateToken(User user, IList<string> roles)
    {
        var key = new SymmetricSecurityKey(
            Encoding.UTF8.GetBytes(_jwtSettings.Value.Secret));

        var credentials = new SigningCredentials(
            key, SecurityAlgorithms.HmacSha256);

        var claims = new List<Claim>
        {
            new Claim(JwtRegisteredClaimNames.Sub, user.Id.ToString()),
            new Claim(JwtRegisteredClaimNames.Email, user.Email),
            new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString()),
            new Claim(ClaimTypes.NameIdentifier, user.Id.ToString())
        };

        claims.AddRange(roles.Select(role => 
            new Claim(ClaimTypes.Role, role)));

        var token = new JwtSecurityToken(
            issuer: _jwtSettings.Value.Issuer,
            audience: _jwtSettings.Value.Audience,
            claims: claims,
            expires: DateTime.UtcNow.Add(_jwtSettings.Value.Expiry),
            signingCredentials: credentials
        );

        return new JwtSecurityTokenHandler().WriteToken(token);
    }
}
```

### JWT Configuration
```json
{
  "JwtSettings": {
    "Secret": "your-256-bit-secret-key-here",
    "Issuer": "https://yourdomain.com",
    "Audience": "https://yourdomain.com",
    "Expiry": "01:00:00",
    "RefreshTokenExpiry": "07:00:00:00"
  }
}
```

### Authorization Policy
```csharp
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("AdminOnly", policy =>
        policy.RequireRole("Admin"));

    options.AddPolicy("CanManageProducts", policy =>
        policy.RequireClaim("Permission", "ManageProducts"));
});
```

## Security Best Practices

1. Use strong secret key (256+ bits)
2. Set appropriate token expiration
3. Implement refresh token rotation
4. Store refresh tokens securely (hashed)
5. Use HTTPS in production
6. Validate all claims
7. Implement rate limiting
8. Log authentication events

## Example Usage

```
Implement JWT authentication with:
- User registration
- User login
- Token generation
- Refresh token support
- Role-based authorization
- Password hashing (BCrypt)
```

I will generate complete JWT authentication implementation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mitkox) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

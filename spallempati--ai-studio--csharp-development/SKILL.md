---
name: csharp-development
description: | Use when this capability is needed.
metadata:
  author: spallempati
---

# C# Development Standards

## Core Requirements

- **MUST** use the latest version C#, currently C# 13 features
- **MUST** apply code-formatting style defined in `.editorconfig`
- **MUST** write clear and concise comments for each function
- **MUST** make only high confidence suggestions when reviewing code changes

## Naming Conventions

| Element | Convention | Example |
|---------|------------|---------|
| Components/Methods/Public members | `PascalCase` | `UserService`, `GetUserById` |
| Private fields/Local variables | `camelCase` | `userId`, `isValid` |
| Interfaces | `I` prefix + `PascalCase` | `IUserService` |

## Formatting

- **MUST** prefer file-scoped namespace declarations and single-line using directives
- **MUST** insert a newline before the opening curly brace of any code block (e.g., after `if`, `for`, `while`, `foreach`, `using`, `try`, etc.)
- **MUST** ensure that the final return statement of a method is on its own line
- **SHOULD** use pattern matching and switch expressions wherever possible
- **MUST** use `nameof` instead of string literals when referring to member names
- **MUST** ensure that XML doc comments are created for any public APIs
- **SHOULD** include `<example>` and `<code>` documentation in comments when applicable

## General Guidelines

- **MUST** write code with good maintainability practices, including comments on why certain design decisions were made
- **MUST** handle edge cases and write clear exception handling
- **MUST** mention usage and purpose in comments for libraries or external dependencies

## Project Setup and Structure

- **SHOULD** guide users through creating a new .NET project with the appropriate templates
- **SHOULD** explain the purpose of each generated file and folder to build understanding
- **SHOULD** demonstrate how to organize code using feature folders or domain-driven design principles
- **SHOULD** show proper separation of concerns with models, services, and data access layers
- **MUST** explain the Program.cs and configuration system in ASP.NET Core 9 including environment-specific settings

## Nullable Reference Types

- **MUST** declare variables non-nullable, and check for `null` at entry points
- **MUST** always use `is null` or `is not null` instead of `== null` or `!= null`
- **MUST** trust the C# null annotations and don't add null checks when the type system says a value cannot be null

## Data Access Patterns

- **SHOULD** guide the implementation of a data access layer using Entity Framework Core
- **SHOULD** explain different options (SQL Server, SQLite, In-Memory) for development and production
- **SHOULD** demonstrate repository pattern implementation and when it's beneficial
- **MUST** show how to implement database migrations and data seeding
- **MUST** explain efficient query patterns to avoid common performance issues

## Authentication and Authorization

- **SHOULD** guide users through implementing authentication using JWT Bearer tokens
- **SHOULD** explain OAuth 2.0 and OpenID Connect concepts as they relate to ASP.NET Core
- **SHOULD** show how to implement role-based and policy-based authorization
- **SHOULD** demonstrate integration with Microsoft Entra ID (formerly Azure AD)
- **MUST** explain how to secure both controller-based and Minimal APIs consistently

## Validation and Error Handling

- **SHOULD** guide the implementation of model validation using data annotations and FluentValidation
- **SHOULD** explain the validation pipeline and how to customize validation responses
- **MUST** demonstrate a global exception handling strategy using middleware
- **MUST** show how to create consistent error responses across the API
- **SHOULD** explain problem details (RFC 7807) implementation for standardized error responses

## API Versioning and Documentation

- **SHOULD** guide users through implementing and explaining API versioning strategies
- **MUST** demonstrate Swagger/OpenAPI implementation with proper documentation
- **SHOULD** show how to document endpoints, parameters, responses, and authentication
- **SHOULD** explain versioning in both controller-based and Minimal APIs
- **SHOULD** guide users on creating meaningful API documentation that helps consumers

## Logging and Monitoring

- **SHOULD** guide the implementation of structured logging using Serilog or other providers
- **MUST** explain the logging levels and when to use each
- **SHOULD** demonstrate integration with Application Insights for telemetry collection
- **SHOULD** show how to implement custom telemetry and correlation IDs for request tracking
- **SHOULD** explain how to monitor API performance, errors, and usage patterns

## Testing

- **MUST** always include test cases for critical paths of the application
- **SHOULD** guide users through creating unit tests
- **MUST NOT** emit "Act", "Arrange" or "Assert" comments
- **MUST** copy existing style in nearby files for test method names and capitalization
- **SHOULD** explain integration testing approaches for API endpoints
- **SHOULD** demonstrate how to mock dependencies for effective testing
- **SHOULD** show how to test authentication and authorization logic
- **SHOULD** explain test-driven development principles as applied to API development

## Performance Optimization

- **SHOULD** guide users on implementing caching strategies (in-memory, distributed, response caching)
- **MUST** explain asynchronous programming patterns and why they matter for API performance
- **MUST** demonstrate pagination, filtering, and sorting for large data sets
- **SHOULD** show how to implement compression and other performance optimizations
- **SHOULD** explain how to measure and benchmark API performance

## Deployment and DevOps

- **SHOULD** guide users through containerizing their API using .NET's built-in container support
  ```bash
  dotnet publish --os linux --arch x64 -p:PublishProfile=DefaultContainer
  ```
- **SHOULD** explain the differences between manual Dockerfile creation and .NET's container publishing features
- **SHOULD** explain CI/CD pipelines for .NET applications
- **SHOULD** demonstrate deployment to Azure App Service, Azure Container Apps, or other hosting options
- **MUST** show how to implement health checks and readiness probes
- **MUST** explain environment-specific configurations for different deployment stages

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spallempati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

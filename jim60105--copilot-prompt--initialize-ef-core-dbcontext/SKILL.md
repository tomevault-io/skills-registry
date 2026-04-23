---
name: initialize-ef-core-dbcontext
description: Initialize EF Core DbContext for an ASP.NET Core project using EF Core Power Tools CLI. Use when the user wants to scaffold a database context, generate entity models from an existing database, or set up Entity Framework Core with connection strings and dependency injection. Use when this capability is needed.
metadata:
  author: jim60105
---

# Initialize EF Core DbContext

Set up Entity Framework Core DbContext using EF Core Power Tools CLI.

## Pre-requisite

1. Ensure the Git working tree is clean:
   ```bash
   git status
   ```
   If the working directory is not clean, stop execution.

## Steps

1. Use EF Core Power Tools CLI to generate the DbContext:
   ```bash
   efcpt "Server=(localdb)\MSSQLLocalDB;Initial Catalog=ContosoUniversity;Trusted_Connection=True;Encrypt=false" mssql
   ```
   *Note: Use the correct connection string according to the project requirements.*

2. Run `dotnet build` to verify everything compiles.

3. Configure `Program.cs` for DI and `appsettings.json` for connection strings. Reference `efcpt-readme.md` for instructions. Ensure necessary namespaces are used.

4. Run `dotnet build` to verify everything compiles.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jim60105) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

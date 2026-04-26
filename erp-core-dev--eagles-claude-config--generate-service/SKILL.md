---
name: generate-service
description: Generate .NET service with interface, implementation, and DI registration Use when this capability is needed.
metadata:
  author: erp-core-dev
---

# Generate .NET Service

Generate a service layer with interface, implementation, validation, and DI registration.

## What To Do

1. **Generate IService interface** with async methods
2. **Generate Service implementation** with constructor injection
3. **Add validation logic** in service methods
4. **Register in DI** (Program.cs): `builder.Services.AddScoped<IXxxService, XxxService>()`
5. **Generate unit tests** with mocked repository

## Arguments
- `<service-name>`: Service name (without "Service" suffix)
- `--with-caching`: Add IMemoryCache integration
- `--with-validation`: Add FluentValidation rules

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erp-core-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

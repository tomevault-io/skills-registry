---
name: aspire
description: Manage .NET Aspire applications — run, add integrations, publish, deploy, and configure Use when this capability is needed.
metadata:
  author: vivecuervo7
---

# .NET Aspire CLI

Manage Aspire-based applications using the `aspire` CLI. Always pass `--non-interactive` to avoid interactive prompts.

## Commands

### Run the application stack

```bash
aspire run --non-interactive
# With specific AppHost project
aspire run --project path/to/AppHost.csproj --non-interactive
```

### Add an integration

```bash
aspire add <integration> --non-interactive
# Examples
aspire add redis --non-interactive
aspire add postgres --non-interactive
# With specific version
aspire add redis --version 9.2.0 --non-interactive
```

### Publish deployment artifacts (Preview)

```bash
aspire publish --non-interactive
aspire publish --output-path ./artifacts --non-interactive
aspire publish --environment Staging --non-interactive
```

### Deploy (Preview)

```bash
aspire deploy --non-interactive
aspire deploy --environment Production --non-interactive
```

### Execute a pipeline step (Preview)

```bash
aspire do <step> --non-interactive
```

### Update integrations

```bash
aspire update --non-interactive
# Update the CLI itself
aspire update --self
```

### Configuration

```bash
aspire config list
aspire config get <key>
aspire config set <key> <value>
aspire config delete <key>
```

## Notes

- Always use `--non-interactive` to prevent interactive prompts that Claude cannot respond to
- If a project uses Aspire, prefer `aspire run` over `dotnet run` for starting the application
- The `--project` flag can target a specific AppHost when multiple exist

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vivecuervo7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

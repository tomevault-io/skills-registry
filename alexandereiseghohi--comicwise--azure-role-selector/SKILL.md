---
name: azure-role-selector
description: Use 'Azure MCP/documentation' tool to find the minimal role definition that Use when this capability is needed.
metadata:
  author: alexandereiseghohi
---

Use 'Azure MCP/documentation' tool to find the minimal role definition that
matches the desired permissions the user wants to assign to an identity (If no
built-in role matches the desired permissions, use 'Azure
MCP/extension_cli_generate' tool to create a custom role definition with the
desired permissions). Use 'Azure MCP/extension_cli_generate' tool to generate
the CLI commands needed to assign that role to the identity and use the 'Azure
MCP/bicepschema' and the 'Azure MCP/get_bestpractices' tool to provide a Bicep
code snippet for adding the role assignment.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexandereiseghohi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

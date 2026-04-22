---
name: test-data
description: Read and write test data Use when this capability is needed.
metadata:
  author: erymuzuan
---
# Database access use SqlCmd.exe too

## Tools
- **SqlCmd.exe**: for database access
- **server**: -S "(local)\DEV2022"
- **Trust Server Certificate**: -C
- **trusted connection**: -E 
- **database**: -d see database section

## Database 
- **MotoRent**: for pilot group, contains user data, such as `Shop`, `Vehicle` etc.
- **MotoRent**: core database for stafy.my and stafy.co.th group
    - Core database contains `Core` schema objects such as `User`, `Organization` etc.

## Tenant schema
- **MotoRent** is multi-tenant application, where each tenant has it's own schema, the list of all tenants is kept in `Core.Organization` table.
- **Tenant**: Take `Shop` as example, it's table name is `Shop`, and it's schema name is `<tenant>`

## JSON object
- **JSON** column is for serialized JSON data .Net object,as described in `domain.standard` project
- **Entity** JSON data is a serialized object of `Entity` class, `MotoRent.Domain.Entities.<table>`, where `<table>` is the `Entity`
- **Primary key** `<table>Id` format


## Interview
- **Interview** use AskUserQuestion tools`HiroRxERP`
- **Tenant** ask me about the tenant if one not specified, account

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erymuzuan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

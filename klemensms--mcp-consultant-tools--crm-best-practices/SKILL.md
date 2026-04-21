---
name: crm-best-practices
description: Standards and conventions for Microsoft Dynamics 365/CRM customization, including table creation, column naming, option sets, and Power Platform development. Use when working with Dynamics CRM, Dataverse, Power Platform, Power Automate flows, Azure integrations with CRM, creating or modifying CRM entities/tables, writing plugins, or any CRM customization work. Use when this capability is needed.
metadata:
  author: klemensms
---

version: "1.0"
author: Klemens Stelk

# CRM Best Practices

Follow these standards when working with Dynamics 365/CRM, Dataverse, or Power Platform.

## Publisher

**Always use the `sic_` prefix** (SmartImpactCustomer publisher).

## Table Naming (MUST)

- **Never** use 'Organisational' ownership - always use 'User or Team'
- Use **all lowercase** for schema names
- Reference data tables: use `sic_ref_` prefix (e.g., `sic_ref_typeofestablishment`)
- BAU tables: use `sic_` prefix (e.g., `sic_application`)

| Type    | Display Name            | Schema Name                   |
|---------|-------------------------|-------------------------------|
| RefData | Type Of Establishment   | sic_ref_typeofestablishment   |
| BAU     | Application             | sic_application               |

## Column Naming (SHOULD)

- **Lookups**: `sic_{target_table_name}id` (e.g., `sic_contactid` for Contact lookup)
- **All others**: schema name matches field name, all lowercase (e.g., Display: "Start Date" → Schema: `sic_startdate`)
- Avoid booleans unless absolutely necessary
- DateTime: use 'Time Zone Independent' unless multi-timezone CRM

## Option Sets / Status Columns (SHOULD)

- Do **NOT** use OOTB state/status reason unless deactivation or status transitions are part of business process
- **Default to global option sets** for simplicity and reusability
- Use RefData tables only when:
  - Status logic must be data-driven or extensible
  - Additional metadata, transitions, or role-based rules are required
  - Many options exist or different teams use different status sets

## Required Columns for New Tables

All tables (except RefData) must have:
- `sic_updatedbyprocess` (Single line text, 4000 chars) - "This field is updated each time an automated process updates this record."

RefData tables additionally need:
- `sic_startdate` (Date only, TZ independent) - "The date this reference data record started being used."
- `sic_enddate` (Date only, TZ independent) - "The date this reference data record stopped being used."
- `sic_description` (Multi-line plain text, 20,000 chars)
- `sic_code` (Single line text) - "Code to identify the record instead of GUID"

Data migration columns (when applicable):
- `sic_externalkey` - "Unique identifier from original source system"
- `sic_externalsystem` - "Original source system"

## New Table Checklist

1. **Creation**: Set schema name with correct prefix, uncheck unused table features, rename primary column schema to `sic_name`
2. **Columns**: Add client columns per wireframes, add generic columns (updatedbyprocess), add RefData columns if applicable
3. **Customization**: Copy/rename SI-Product forms, enable Power App Grid Control for colored option-set statuses, customize Active records view
4. **Grid Colors**: Orange `ffd175`, Green `8ed483`, Red `ff8c8c`, Grey `d1d1d1`
5. **Views**: Name column first, then relevant columns, created on/by, modified on/by, status
6. **Timeline**: Reduce activity types to <10 for performance
7. **Icon**: Add 16px SVG as web resource
8. **Security**: Add table to MDA, update sitemap, add/update security role

## Validation

Use the `validate-dataverse-best-practices` tool with `publisherPrefix: "sic_"` to check entity compliance.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/klemensms) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

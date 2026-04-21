---
name: leaflink-wholesale
description: Manage wholesale inventory, products, and orders via LeafLink API. Use when this capability is needed.
metadata:
  author: admin-baked
---

# LeafLink Skill

## Capabilities
- **Orders**: View incoming wholesale orders (`leaflink.list_orders`).
- **Products**: List wholesale catalog (`leaflink.list_products`).
- **Inventory**: Update stock levels for specific products (`leaflink.update_inventory`).

## Usage
- Use for "B2B", "Wholesale", or "Distributor" related queries.
- Use `leaflink.update_inventory` when the user explicitly asks to "set stock" or "update quantity".

## Constraints
- Requires user authentication via `requireUser`.
- Requires LeafLink API key in user settings.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/admin-baked) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

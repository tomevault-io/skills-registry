---
name: xstore-theme-options
description: Configure XStore WordPress theme options via WP-CLI. Use when setting brand colors, typography, layout, buttons, breadcrumbs, or WooCommerce settings for XStore theme. Use when this capability is needed.
metadata:
  author: sattip
---

# XStore Theme Options Configuration

Configure XStore theme options via WP-CLI commands.

## Instructions

When this skill is invoked, help the user configure XStore theme options.

### Reference Documentation

Read the theme options reference from `THEME_OPTIONS.md` in this repository for complete option list.

### Common Tasks

**Set Brand Colors:**
```bash
wp option patch update theme_mods_xstore-child activecol '#ff7eb9'
wp option patch update theme_mods_xstore-child light_buttons_bg '{"regular":"#ff7eb9","hover":"#ff65a3"}'
wp option patch update theme_mods_xstore-child light_buttons_color '{"regular":"#ffffff","hover":"#ffffff"}'
```

**Set Typography:**
```bash
wp option patch update theme_mods_xstore-child sfont '{"font-family":"Manrope-Regular","font-size":"17px","line-height":"1.6"}'
wp option patch update theme_mods_xstore-child headings '{"font-family":"Manrope-SemiBold","font-weight":700}'
```

**Set Layout:**
```bash
wp option patch update theme_mods_xstore-child main_layout 'wide'
wp option patch update theme_mods_xstore-child site_width 1170
```

**Set WooCommerce Options:**
```bash
wp option patch update theme_mods_xstore-child products_per_page 12
wp option patch update theme_mods_xstore-child grid_sidebar 'left'
wp option patch update theme_mods_xstore-child product_view 'default'
```

### Workflow

1. Ask the user what theme options they want to configure
2. Reference THEME_OPTIONS.md for available options and valid values
3. Generate the appropriate WP-CLI commands
4. Execute commands via SSH or provide them for manual execution
5. Remind user to clear cache: `wp cache flush`

### Important Notes

- Always backup before changes: `wp db export backup.sql`
- Clear cache after changes: `wp cache flush && wp transient delete --all`
- Use JSON format for complex options (typography, colors with states)
- Colors use hex format: `#ff7eb9`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sattip) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

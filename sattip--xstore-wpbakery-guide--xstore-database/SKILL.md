---
name: xstore-database
description: Database operations for XStore and WPBakery via WP-CLI and SQL. Use when backing up, bulk updating, searching, or performing direct database queries on WordPress with XStore theme. Use when this capability is needed.
metadata:
  author: sattip
---

# XStore Database Operations

Direct database operations for XStore/WPBakery via WP-CLI and SQL.

## Instructions

When this skill is invoked, help the user perform database operations for XStore theme.

### Reference Documentation

Read the database reference from `DATABASE_OPERATIONS.md` in this repository.

### Backup Commands (ALWAYS RUN FIRST)

```bash
# Full database backup
wp db export backup-$(date +%Y%m%d-%H%M%S).sql

# Backup theme options only
wp option get theme_mods_xstore-child --format=json > theme-backup.json
```

### Common Operations

**Get theme options:**
```bash
wp option get theme_mods_xstore-child --format=json
wp option pluck theme_mods_xstore-child activecol
```

**Set theme options:**
```bash
wp option patch update theme_mods_xstore-child KEY 'VALUE'
```

**Create page:**
```bash
wp post create --post_type=page \
  --post_title="Title" \
  --post_content='[vc_row]...[/vc_row]' \
  --post_status=publish \
  --porcelain
```

**Set page meta:**
```bash
wp post meta update PAGE_ID _wpb_vc_js_status 'true'
wp post meta update PAGE_ID _wpb_shortcodes_custom_css 'CSS'
```

### Cache Management

```bash
# Clear all caches (run after any changes)
wp cache flush && wp transient delete --all && wp rewrite flush
```

### Search and Replace

```bash
# Preview changes
wp search-replace 'old-text' 'new-text' --dry-run

# Execute replacement
wp search-replace 'old-text' 'new-text'
```

### Useful Queries

```bash
# Find pages with WPBakery
wp db query "SELECT ID, post_title FROM wp_posts WHERE post_type='page' AND post_content LIKE '%[vc_row%'"

# List static blocks
wp post list --post_type=staticblocks --format=table

# Get page content
wp post get PAGE_ID --field=post_content
```

### Workflow

1. ALWAYS backup first: `wp db export backup.sql`
2. Perform the requested operation
3. Clear caches: `wp cache flush`
4. Verify the changes
5. Provide rollback instructions if needed

### Safety Notes

- Never run destructive commands without backup
- Use `--dry-run` when available to preview changes
- Clear cache after any database changes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sattip) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

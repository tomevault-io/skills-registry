---
name: vaadin-grid-styling
description: Dynamically style Vaadin Grid/DataGrid rows based on entity data. Use this skill when highlighting rows based on conditions (e.g., deleted, overdue, warning states). Use this skill when applying custom CSS to grid rows. Use this skill when working with setPartNameGenerator and ::part() CSS selectors. Use when this capability is needed.
metadata:
  author: torbenmerrald
---

# Vaadin Grid Styling

## When to use this skill

- When highlighting grid rows based on entity state (deleted, overdue, warning, etc.)
- When applying dynamic CSS classes to DataGrid rows
- When using conditional row styling in Jmix/Vaadin views
- When working with Shadow DOM styling for Vaadin components

## Instructions

### Key Concept: Shadow DOM

Vaadin Grid uses Shadow DOM, which means **regular CSS selectors don't work**. You must use:
- Java: `setPartNameGenerator()` (NOT `setClassNameGenerator()`)
- CSS: `::part()` selectors

### Java Implementation

In your view controller, use `setPartNameGenerator()` to assign part names based on entity data:

```java
@Subscribe
public void onInit(final InitEvent event) {
    dataGrid.setPartNameGenerator(entity -> {
        if (entity.getDeletedDate() != null) {
            return "deleted-row";
        }
        if (entity.isOverdue()) {
            return "overdue-row";
        }
        if (entity.needsAttention()) {
            return "warning-row";
        }
        return null;  // No special styling
    });
}
```

### CSS Implementation

Add styles to `frontend/themes/sm/styles.css` using `::part()` selectors:

```css
/* Deleted/inactive rows - faded appearance */
vaadin-grid::part(deleted-row) {
    opacity: 0.5;
    background: var(--lumo-contrast-10pct);
}

/* Overdue/error rows - red highlight */
vaadin-grid::part(overdue-row) {
    background: linear-gradient(
        var(--lumo-error-color-10pct),
        var(--lumo-error-color-10pct)
    ) var(--lumo-base-color);
}

/* Warning rows - yellow highlight */
vaadin-grid::part(warning-row) {
    background: linear-gradient(
        var(--lumo-warning-color-10pct),
        var(--lumo-warning-color-10pct)
    ) var(--lumo-base-color);
}

/* Success/good rows - green highlight */
vaadin-grid::part(success-row) {
    background: linear-gradient(
        var(--lumo-success-color-10pct),
        var(--lumo-success-color-10pct)
    ) var(--lumo-base-color);
}
```

### Common Mistakes to Avoid

1. **Wrong method**: Using `setClassNameGenerator()` instead of `setPartNameGenerator()`
2. **Wrong CSS selector**: Using `.class-name` instead of `::part(part-name)`
3. **Wrong CSS target**: Using `tr.class-name` or other regular selectors

### Column-Specific Styling

For individual columns, use `setPartNameGenerator()` on the column:

```java
grid.addColumn(Entity::getRating)
    .setHeader("Rating")
    .setPartNameGenerator(entity -> "font-weight-bold");
```

```css
vaadin-grid::part(font-weight-bold) {
    font-weight: bold;
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/torbenmerrald) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

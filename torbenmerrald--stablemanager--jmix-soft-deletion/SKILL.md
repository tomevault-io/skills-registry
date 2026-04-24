---
name: jmix-soft-deletion
description: Work with soft-deleted entities in Jmix applications. Use this skill when implementing features that need to show or hide soft-deleted records. Use this skill when bypassing soft deletion filtering to include deleted entities in queries. Use this skill when working with @DeletedDate and @DeletedBy annotations. Use this skill when implementing "show deleted" toggles in list views. Use when this capability is needed.
metadata:
  author: torbenmerrald
---

# Jmix Soft Deletion

## When to use this skill

- When implementing a toggle to show/hide soft-deleted entities in a view
- When writing queries that need to include soft-deleted records
- When using LoadContext hints to control soft deletion filtering
- When working with entities that have @DeletedDate and @DeletedBy annotations
- When implementing restore functionality for soft-deleted entities

## Instructions

### Entity Setup

Entities with soft delete support have these annotations:

```java
@DeletedBy
@Column(name = "DELETED_BY")
private String deletedBy;

@DeletedDate
@Column(name = "DELETED_DATE")
private OffsetDateTime deletedDate;
```

### Including Soft-Deleted Entities in Queries

Use `PersistenceHints.SOFT_DELETION` with a `LoadContext` or `loadDelegate`:

```java
import io.jmix.data.PersistenceHints;  // IMPORTANT: NOT io.jmix.core

@Install(to = "entityDl", target = Target.DATA_LOADER)
private List<Entity> loadDelegate(LoadContext<Entity> loadContext) {
    boolean showDeleted = Boolean.TRUE.equals(showDeletedCheckbox.getValue());
    loadContext.setHint(PersistenceHints.SOFT_DELETION, !showDeleted);
    return dataManager.loadList(loadContext);
}
```

- `SOFT_DELETION = true` (default): Filters out deleted entities
- `SOFT_DELETION = false`: Includes deleted entities

### Important Considerations

1. **Caching**: Remove `cacheable="true"` from XML loaders when dynamically toggling soft deletion, as cached results may ignore hint changes.

2. **Reload on toggle**: When the user toggles the show/hide deleted checkbox, call `loader.load()` to refresh the data:

```java
@Subscribe("showDeletedCheckbox")
public void onShowDeletedCheckboxChange(AbstractField.ComponentValueChangeEvent<JmixCheckbox, Boolean> event) {
    entityDl.load();
}
```

3. **Visual distinction**: Combine with vaadin-grid-styling skill to visually distinguish deleted entities (e.g., faded/gray appearance).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/torbenmerrald) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

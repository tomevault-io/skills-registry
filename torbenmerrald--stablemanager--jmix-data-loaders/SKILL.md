---
name: jmix-data-loaders
description: Work with Jmix CollectionLoader, loadDelegate, and LoadContext for custom data loading. Use this skill when implementing custom query logic in views. Use this skill when applying hints to control query behavior. Use this skill when filtering or transforming loaded data programmatically. Use when this capability is needed.
metadata:
  author: torbenmerrald
---

# Jmix Data Loaders

## When to use this skill

- When implementing custom data loading logic in Jmix views
- When using loadDelegate to override default loader behavior
- When applying hints to LoadContext (soft deletion, caching, etc.)
- When dynamically modifying queries based on UI state
- When combining multiple data sources or transforming loaded data

## Instructions

### Basic Loader Setup (XML)

```xml
<data>
    <collection id="entitiesDc" class="com.example.Entity">
        <fetchPlan extends="_base">
            <property name="relation" fetchPlan="_base"/>
        </fetchPlan>
        <loader id="entitiesDl" readOnly="true">
            <query>
                <![CDATA[select e from Entity e order by e.name]]>
            </query>
        </loader>
    </collection>
</data>
```

### Using loadDelegate for Custom Loading

Override the default loading behavior with `@Install`:

```java
@ViewComponent
private CollectionLoader<Entity> entitiesDl;

@Autowired
private DataManager dataManager;

@Install(to = "entitiesDl", target = Target.DATA_LOADER)
private List<Entity> loadDelegate(LoadContext<Entity> loadContext) {
    // Modify the LoadContext as needed
    loadContext.setHint(PersistenceHints.SOFT_DELETION, false);

    // Load using DataManager
    return dataManager.loadList(loadContext);
}
```

### Common LoadContext Hints

```java
import io.jmix.data.PersistenceHints;

// Include soft-deleted entities
loadContext.setHint(PersistenceHints.SOFT_DELETION, false);

// Enable query caching
loadContext.setHint(PersistenceHints.CACHEABLE, true);
```

### Dynamic Query Modification

```java
@Install(to = "entitiesDl", target = Target.DATA_LOADER)
private List<Entity> loadDelegate(LoadContext<Entity> loadContext) {
    LoadContext.Query query = loadContext.getQuery();

    // Modify query string
    if (someCondition) {
        query.setQueryString("select e from Entity e where e.active = true");
    }

    // Add parameters
    query.setParameter("status", selectedStatus);

    return dataManager.loadList(loadContext);
}
```

### Triggering Reload

```java
@ViewComponent
private CollectionLoader<Entity> entitiesDl;

// Reload data when filter changes
@Subscribe("filterField")
public void onFilterChange(ValueChangeEvent event) {
    entitiesDl.load();
}
```

### Important Considerations

1. **Caching**: When using dynamic hints, remove `cacheable="true"` from XML loader to ensure fresh data.

2. **Parameters**: Set parameters before calling `load()`:
   ```java
   entitiesDl.setParameter("status", selectedStatus);
   entitiesDl.load();
   ```

3. **DataLoadCoordinator**: If using `<dataLoadCoordinator auto="true"/>`, the loader triggers automatically. For manual control, use `auto="false"` or call `load()` explicitly.

4. **Fetch Plans**: The LoadContext inherits the fetch plan from XML. Modify only if needed:
   ```java
   loadContext.setFetchPlan(fetchPlans.builder(Entity.class)
       .addAll()
       .build());
   ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/torbenmerrald) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

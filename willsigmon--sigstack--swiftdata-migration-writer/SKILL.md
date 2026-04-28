---
name: swiftdata-migration-writer
description: Write UserDefaults to SwiftData migration logic for Leavn app with data preservation, rollback, and validation Use when this capability is needed.
metadata:
  author: willsigmon
---

# SwiftData Migration Writer

Create migration from UserDefaults to SwiftData:

1. **Map keys to entity fields**
2. **Write migration method**:
   ```swift
   func migrateXIfNeeded() async throws {
       guard !hasMigrated("X") else { return }
       // Read UserDefaults
       // Create/update entity
       // Archive old keys
       // Mark migrated
   }
   ```

3. **Add to PreferencesStore extension**
4. **Call on first load**
5. **Test data preservation**

Use when: Creating SwiftData entities, migrating preferences, data persistence

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willsigmon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->

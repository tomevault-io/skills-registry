---
name: deploy
description: Build und deploy zu Firebase (Functions + Hosting) Use when this capability is needed.
metadata:
  author: wilddragonking
---

# Deploy zu Firebase

Führe folgende Schritte aus:

1. **Site bauen:**
   ```bash
   cd /Users/lbuettge/Projects/personal/RehaSport/site && npm run build
   ```

2. **Zu Firebase deployen:**
   ```bash
   cd /Users/lbuettge/Projects/personal/RehaSport && npx firebase deploy
   ```

3. **Ergebnis melden:** Gib die Hosting-URL aus und bestätige den erfolgreichen Deploy.

Falls ein Build-Fehler auftritt, zeige die Fehlermeldung und biete an, das Problem zu beheben.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wilddragonking) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

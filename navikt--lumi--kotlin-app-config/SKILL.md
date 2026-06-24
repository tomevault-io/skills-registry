---
name: kotlin-app-config
description: Sealed class configuration pattern for Kotlin applications with environment-specific settings Use when this capability is needed.
metadata:
  author: navikt
---

# Kotlin Application Configuration Skill

NOTE: `lumi-api` uses `ServerEnv` (see `no.nav.lumi.config.ServerEnv`) for type-safe configuration and local defaults. Prefer that over introducing a new sealed-environment framework.

## Use `ServerEnv` (repo standard)

### Pattern

- Keep all environment variable access centralized in `ServerEnv`.
- Prefer explicit, named properties over passing `Map<String, String>` around.
- Use `NAIS_CLUSTER_NAME` to switch between local and NAIS defaults.

### Example usage

```kotlin
import no.nav.lumi.config.ServerEnv

fun main() {
    val env = ServerEnv.fromEnv()
    // Pass env values into module/config installation rather than reading System.getenv() everywhere.
}
```

## Boundaries

### ✅ Always

- Add new environment variables to `ServerEnv` and document the local default if applicable.

### ⚠️ Ask First

- Introducing new config frameworks (Konfig, sealed env abstractions) in this repo.

### 🚫 Never

- Read env vars ad-hoc across the codebase when `ServerEnv` is the established pattern.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/navikt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

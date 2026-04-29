---
name: kotlin-multiplatform
description: Kotlin Multiplatform - shared code, expect/actual, iOS integration Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Kotlin Multiplatform Skill

Build cross-platform applications with shared Kotlin code.

## Topics Covered

### Project Setup
```kotlin
kotlin {
    androidTarget()
    listOf(iosX64(), iosArm64(), iosSimulatorArm64()).forEach {
        it.binaries.framework { baseName = "Shared"; isStatic = true }
    }
    sourceSets {
        commonMain.dependencies {
            implementation("io.ktor:ktor-client-core:2.3.8")
        }
        androidMain.dependencies { implementation("io.ktor:ktor-client-okhttp:2.3.8") }
        iosMain.dependencies { implementation("io.ktor:ktor-client-darwin:2.3.8") }
    }
}
```

### expect/actual
```kotlin
// commonMain
expect class SecureStorage { fun get(key: String): String? }

// androidMain
actual class SecureStorage { actual fun get(key: String) = prefs.getString(key, null) }

// iosMain
actual class SecureStorage { actual fun get(key: String) = KeychainWrapper.get(key) }
```

## Troubleshooting

| Issue | Resolution |
|-------|------------|
| "No actual for expect" | Add implementation in platform source set |
| iOS framework not found | Run linkDebugFrameworkIos task |

## Usage
```
Skill("kotlin-multiplatform")
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

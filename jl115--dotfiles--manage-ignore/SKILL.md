---
name: manage-ignore
description: Analyzes project structure (Node, Flutter, Mobile) and populates .opencodeignore with best-practice rules.
metadata:
  author: jl115
---

## What I do

I analyze the current project's file structure to detect technologies (Node.js, Flutter, Android, iOS) and ensure the `.opencodeignore` file exists and contains the necessary exclusion rules.

## How to use me

1.  **Analyze**: List the files in the root directory to detect the technology stack.
    - **Node.js**: Look for `package.json`, `node_modules`, `yarn.lock`, or `pnpm-lock.yaml`.
    - **Flutter/Dart**: Look for `pubspec.yaml`, `lib/main.dart`, or `.dart_tool`.
    - **Mobile**: Look for `android/` or `ios/` directories.
2.  **Read**: Read the existing `.opencodeignore` file (if it exists).
3.  **Update**: Append **missing** rules from the "Master Template" below. **Do not** remove existing rules or overwrite the file entirely; only append new sections or lines that are not present.
4.  **Report**: Tell the user which rules or sections were added.

## Master Template

Use the following rules based on the detection logic above.

### Always Include (Base Rules)

```text
# ==========================================
# Environment & Secrets (CRITICAL)
# ==========================================
.env
.env.*
!.env.example
secrets.yaml
*-local.js

# ==========================================
# IDE & System Trash
# ==========================================
.DS_Store
Thumbs.db
.vscode/
.idea/
*.swp
*.log
.history/
```

### Include if Node.js / TypeScript detected

````
```Plaintext
# ==========================================
# JavaScript / TypeScript (Node.js)
# ==========================================
node_modules/
dist/
build/
out/
.npm/
.eslintcache
.prettiercache
tsbuildinfo
package-lock.json
yarn.lock
pnpm-lock.yaml
````

### Include if Flutter / Dart detected

```
Plaintext
# ==========================================
# Flutter / Dart
# ==========================================
.dart_tool/
.flutter-plugins
.flutter-plugins-dependencies
.packages
build/
# If you use generated code (Freezed/JSON Serializable)
# and don't want the AI to "read" it:
*.freezed.dart
*.g.dart
```

### Include if Mobile (Android/iOS) detected

```
Plaintext
# ==========================================
# Mobile Specific (iOS/Android)
# ==========================================
.gradle/
local.properties
*.keystore
*.jks
.cxx/
android/app/debug/
android/app/release/
ios/Pods/
ios/.symlinks/
ios/Flutter/Generated.xcconfig
ios/Flutter/flutter_export_environment.sh
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jl115) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

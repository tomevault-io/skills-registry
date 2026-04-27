---
name: ios-scaffold
description: Scaffold a SwiftUI iOS app (iPhone + iPad) with offline-first persistence, push notifications plumbing, SwiftFormat + SwiftLint, CI, and optional GitHub repo creation. Use when this capability is needed.
metadata:
  author: edfenton
---

## Purpose
Create a fully working iOS repo with SwiftUI + MVVM, offline-first persistence, push notification plumbing (compiles without APNs), environment configs, tooling, and CI.

## Arguments
- `app-name` — Project folder/app name (default: "App")
- `--github` — Create GitHub repo and push (requires `gh auth`)
- `--public` — Make GitHub repo public (default: private)
- `--org <n>` — Create under org instead of personal account
- `--repo <n>` — Override repo name (default: app-name)
- `--no-push` — Create repo and commit but don't push

## Template-first rule
Copy files from `templates/` into the project, preserving folder structure. Generate content for empty placeholder files following ios-std conventions.

**Existing directory handling:** If the target directory already exists (e.g., running in the current directory), scaffold files directly into it instead of creating a subdirectory. Check for `--repo` flag to determine the repo name separately from the directory.

Templates provide:
- Tool configs (SwiftFormat, SwiftLint, xcconfig)
- XcodeGen project spec (project.yml)
- CI workflow
- Scripts (format.sh, lint.sh)
- Documentation (build-and-run.md, decisions.md)
- CLAUDE.md for the new repo
- VS Code workspace settings and extension recommendations

## What gets created

```
<app-name>/
├── App/Sources/
│   ├── AppEntry/             # App entry point
│   ├── Features/Home/        # Example feature (View + ViewModel)
│   ├── UIComponents/         # Shared UI primitives
│   ├── Models/               # SwiftData models
│   ├── Services/
│   │   ├── Persistence/      # PersistenceStore protocol + SwiftDataStore
│   │   ├── Notifications/    # Push plumbing (stubbed)
│   │   ├── Config/           # Environment
│   │   └── Logging/          # AppLogger
│   └── Infrastructure/       # DependencyContainer
├── Tests/
│   ├── UnitTests/            # ViewModel, persistence, notification tests
│   └── UITests/              # App launch test
├── Config/                   # Base.xcconfig, NonProd.xcconfig, Prod.xcconfig
├── scripts/                  # format.sh, lint.sh
├── .github/workflows/ci.yml
├── .vscode/                  # VS Code workspace config (from templates)
│   ├── settings.json
│   └── extensions.json
├── Docs/                     # build-and-run.md, decisions.md, PRD-TEMPLATE.md
├── project.yml               # XcodeGen spec (generates .xcodeproj)
└── CLAUDE.md
```

## Prerequisites

Before running scaffold steps, verify required tools are installed. Stop with an error if any required tool is missing.

**Required:**
- `xcode-select -p` — Xcode Command Line Tools
- `which xcodegen` — XcodeGen (install: `brew install xcodegen`)
- `which swiftlint` — SwiftLint (install: `brew install swiftlint`)
- `which swiftformat` — SwiftFormat (install: `brew install swiftformat`)

**Optional (warn if missing):**
- `which gitleaks` — gitleaks for secret scanning in git hooks (install: `brew install gitleaks`)

If any required tool is missing, print which tools are missing with install instructions and stop. Do not proceed with scaffolding.

## Key requirements

### Architecture
- Views contain no business logic
- ViewModels are `@MainActor`, expose state via `@Published`, intents as methods
- Services injected via initializers
- Persistence boundary: ViewModels use `PersistenceStore` protocol, never SwiftData directly

### Push notifications
- Must compile without APNs entitlements
- Permission request works
- Payloads validated before routing

### Environments
- Non-prod + prod via xcconfig
- Non-prod shows environment indicator in UI

### Xcode project (via XcodeGen)
- Install XcodeGen if missing: `brew install xcodegen`
- Copy `project.yml` template, replacing `AppName` with actual app name
- Run `xcodegen generate` to create `.xcodeproj` (gitignored — regenerated from project.yml)
- Targets: App, UnitTests, UITests
- Configurations: NonProd-Debug, NonProd-Release, Prod-Debug, Prod-Release (wired to xcconfig files)
- Schemes: AppName-NonProd (default, includes all tests), AppName-Prod

## Template placeholders
All template files use `AppName` as a placeholder. Replace with the actual app name (PascalCase) when scaffolding:
- `project.yml` — project name, target names, scheme names
- `ci.yml` — project file name, scheme name
- Source files — struct/class names, `@testable import`

## Quality gates (should pass)
```bash
xcodegen generate     # Generate .xcodeproj from project.yml
./scripts/format.sh   # SwiftFormat
./scripts/lint.sh     # SwiftLint --strict
xcodebuild build -project <AppName>.xcodeproj -scheme <AppName>-NonProd -destination 'generic/platform=iOS Simulator' -configuration NonProd-Debug CODE_SIGNING_ALLOWED=NO COMPILATION_CACHE_ENABLE_CACHING=YES
xcodebuild test -project <AppName>.xcodeproj -scheme <AppName>-NonProd -destination 'platform=iOS Simulator,name=<available iPhone>' -configuration NonProd-Debug CODE_SIGNING_ALLOWED=NO COMPILATION_CACHE_ENABLE_CACHING=YES
```
**Note:** Simulator names vary by Xcode version (e.g., Xcode 26.2 has iPhone 17 series). Use `xcrun simctl list devices available` to find an available iPhone simulator. For builds, `generic/platform=iOS Simulator` avoids needing a specific simulator. CI runs on macOS 26 runners.

## GitHub setup (if --github)
- Requires `gh auth status` to succeed
- Creates repo: `gh repo create <owner>/<repo> --<visibility> --source . --remote origin`
- Commits and pushes unless `--no-push`

## Post-scaffold: GitHub security (recommended)

After scaffolding, run these skills to complete GitHub security setup:

1. **`/github-hooks --platform ios`** — Install local Git hooks
   - Direct git hooks in `.githooks/` directory
   - Pre-commit: SwiftLint, SwiftFormat, gitleaks secret scan
   - Commit-msg: conventional commits enforcement
   - Pre-push: run xcodebuild test
   - Includes `scripts/install-hooks.sh` for setup

2. **`/github-secure`** (if `--github` was used) — Configure repo security
   - Branch protection rules
   - Dependabot configuration (for Swift Package Manager)
   - CodeQL security scanning (Swift)
   - CODEOWNERS, SECURITY.md, PR templates

These are invoked automatically by `/ios-kit`.

## Output
Summarize: structure created, build/run steps, tooling included, env switching, push plumbing notes, GitHub status if applicable, **and remind to run github-hooks/github-secure if not using ios-kit**.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/edfenton) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

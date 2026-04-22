---
name: project-structure
description: | Use when this capability is needed.
metadata:
  author: kubrickcode
---

# Project Structure Guide

## Monorepo

```
project-root/
├── src/                         # All services/apps
├── infra/                       # Shared infrastructure
├── docs/                        # Documentation
├── .devcontainer/               # Dev Container configuration
├── .github/                     # Workflows, templates
├── .vscode/                     # VSCode settings
├── .claude/                     # Claude settings
├── .gemini/                     # Gemini settings
├── package.json                 # Root package.json. For releases, version management
├── go.work                      # Go workspace (when using Go)
├── justfile                     # Just task runner
├── .gitignore
├── .prettierrc
├── .prettierignore
└── README.md
```

## NestJS

```
project-root/
├── src/
│   ├── domains/
│   ├── common/
│   ├── config/
│   ├── database/
│   ├── app.module.ts
│   └── main.ts
├── tests/
├── package.json
└── tsconfig.json
```

## React

```
project-root/
├── src/
│   ├── pages/              # Page modules
│   ├── domains/            # Domain-shared code
│   ├── components/         # Common UI components
│   ├── layouts/            # Layout-related
│   ├── libs/               # Feature libraries (auth, api, theme)
│   ├── shared/             # Pure utilities
│   ├── app.tsx
│   └── main.tsx
├── public/
├── package.json
├── vite.config.ts
└── tsconfig.json
```

## Next.js

```
project-root/
├── app/
│   ├── (routes)/           # Pages (route groups)
│   ├── actions/            # Server Actions (internal mutations)
│   └── api/                # API Routes (external integrations only)
├── components/             # Shared components
├── lib/                    # Utilities and clients
├── public/                 # Static assets
├── middleware.ts           # Edge/Node.js middleware
├── next.config.js
├── package.json
└── tsconfig.json
```

## Go

```
project-root/
├── cmd/                    # Execution entry points (main.go)
├── internal/               # Private packages
├── pkg/                    # Public packages
├── configs/                # Configuration files
├── scripts/                # Utility scripts
├── tests/                  # Integration tests
├── docs/                   # Documentation
├── go.mod
└── go.sum
```

## NPM

```
project-root/
├── cli/                        # CLI execution entry point
├── internal/                   # Private packages
├── pkg/                        # Public packages
├── configs/                    # Configuration files
├── scripts/                    # Utility scripts
├── tests/                      # Integration tests
├── docs/                       # Documentation
├── dist/                       # Build artifacts
├── package.json
├── tsconfig.json
└── README.md
```

## IDE Extension

```
project-root/
├── extension/                   # Extension entry point (activate/deactivate)
├── internal/                    # Private packages
├── pkg/                         # Public packages
├── view/                        # WebView (if applicable)
├── configs/                     # Configuration files
├── scripts/                     # Utility scripts
├── tests/                       # Integration tests
├── public/                      # Static resources (icons, etc.)
├── dist/                        # Build artifacts
├── package.json
├── tsconfig.json
└── .vscodeignore
```

## Chrome Extension

```
project-root/
├── background/                  # Service Worker (Background Script)
├── content/                     # Content Scripts
├── popup/                       # Popup (Extension UI)
├── internal/                    # Private packages
├── pkg/                         # Public packages
├── configs/                     # Configuration files
├── scripts/                     # Utility scripts
├── tests/                       # Integration tests
├── public/                      # Static resources
├── dist/                        # Build artifacts
├── package.json
└── tsconfig.json
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kubrickcode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

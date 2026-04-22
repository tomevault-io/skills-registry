---
name: tea
description: > Use when this capability is needed.
metadata:
  author: hay-kot
---

# Tea CLI and Gitea API

Guidelines for working with Gitea/Forgejo using the tea CLI and direct API calls.

## API Documentation

OpenAPI/Swagger specification: https://gitea.kotel.app/swagger.v1.json

Use WebFetch to retrieve endpoint details when needed.

## Tea CLI Commands

Common tea commands for repository management:

```bash
tea repo list                    # List repositories
tea repo view owner/repo         # View repository details
tea pr list                      # List pull requests
tea pr view NUMBER               # View PR details
tea issue list                   # List issues
tea issue view NUMBER            # View issue details
tea release list                 # List releases
```

## Direct API Access

Use `teaapi` to make authenticated API calls with token redaction:

```bash
teaapi curl https://gitea.kotel.app/api/v1/user
teaapi curl https://gitea.kotel.app/api/v1/repos/owner/repo
teaapi curl -X POST -d '{"title":"test"}' https://gitea.kotel.app/api/v1/repos/owner/repo/issues
```

The `teaapi` wrapper:
- Injects the token from tea config as `Authorization: token <TOKEN>`
- Redacts the token value from all output
- Blocks DELETE requests by default (use `--allow-delete` flag to permit, requires approval)
- Supports all curl options

## Common API Endpoints

```bash
# User info
teaapi curl https://gitea.kotel.app/api/v1/user

# Repository
teaapi curl https://gitea.kotel.app/api/v1/repos/OWNER/REPO
teaapi curl https://gitea.kotel.app/api/v1/repos/OWNER/REPO/branches
teaapi curl https://gitea.kotel.app/api/v1/repos/OWNER/REPO/pulls
teaapi curl https://gitea.kotel.app/api/v1/repos/OWNER/REPO/issues

# Search
teaapi curl "https://gitea.kotel.app/api/v1/repos/search?q=QUERY"

# Create issue
teaapi curl -X POST -H "Content-Type: application/json" \
  -d '{"title":"Issue title","body":"Description"}' \
  https://gitea.kotel.app/api/v1/repos/OWNER/REPO/issues

# Create PR
teaapi curl -X POST -H "Content-Type: application/json" \
  -d '{"title":"PR title","head":"branch","base":"main"}' \
  https://gitea.kotel.app/api/v1/repos/OWNER/REPO/pulls
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hay-kot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->

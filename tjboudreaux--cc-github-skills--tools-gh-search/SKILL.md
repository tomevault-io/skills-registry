---
name: tools-gh-search
description: Unified GitHub search across code, commits, issues, PRs, and repos using the gh CLI. Find patterns, track bugs, evaluate dependencies, and monitor changes. Use when this capability is needed.
metadata:
  author: tjboudreaux
---

# GitHub Search

## Overview
Search across all GitHub entity types from the terminal using `gh search`. Use this for library evaluation, debugging, dependency triage, security monitoring, and learning from real-world code.

## Code Search

Find real-world usage patterns, API examples, and implementation references.

### Core Commands
```bash
# Search code across GitHub
gh search code "useCallback" --language=typescript --limit 30

# Search in specific repo
gh search code "createContext" --repo=facebook/react --limit 20

# Search with file extension or path
gh search code "jest.config" --extension=ts
gh search code "middleware" --path=src --language=javascript

# Search in org repos
gh search code "middleware" --owner=expressjs --limit 30
```

### Pattern Discovery
```bash
# Find how an API is used
gh search code "axios.interceptors" --language=typescript --limit 30

# Find configuration patterns
gh search code "tsconfig.json compilerOptions strict" --limit 20

# Find import/usage patterns
gh search code "from '@tanstack/react-query'" --language=typescript --limit 30

# Find error handling patterns
gh search code "try catch axios" --language=typescript --limit 20
```

### Learning a New Library
```bash
# Step 1: Find basic usage
gh search code "import { trpc }" --language=typescript --limit 20
# Step 2: Find initialization
gh search code "createTRPCRouter" --language=typescript --limit 20
# Step 3: Find error handling
gh search code "TRPCError" --language=typescript --limit 20
# Step 4: Find testing approaches
gh search code "trpc" --path=test --language=typescript --limit 15
```

### JSON Output
```bash
gh search code "prisma migrate" --json path,repository,textMatches --limit 10
gh search code "useEffect" --language=typescript --json path,repository --jq '.[] | "\(.repository.fullName)/\(.path)"'
```

## Commit Search

Track change history, find when bugs were introduced, and understand project evolution.

### Core Commands
```bash
# Search commits across GitHub
gh search commits "fix memory leak" --limit 30

# Search in specific repo
gh search commits "refactor" --repo=facebook/react --limit 20

# Search by author
gh search commits --repo=nodejs/node --author=addaleax --limit 30

# Search by date range
gh search commits "performance" --repo=vercel/next.js --committer-date=">$(date -v-30d +%Y-%m-%d)" --limit 30
```

### Bug Tracking
```bash
# Find bug fixes
gh search commits "fix bug" --repo=prisma/prisma --limit 30

# Find commits fixing specific issues
gh search commits "fixes #123" --repo=owner/repo --limit 20

# Find reverts (indicate problems)
gh search commits "revert" --repo=webpack/webpack --limit 20

# Find security fixes
gh search commits "CVE" --repo=urllib3/urllib3 --limit 20
```

### Understanding Feature Evolution
```bash
FEATURE="streaming"
gh search commits "$FEATURE" --repo=vercel/next.js --sort=committer-date --order=asc --limit 30
gh search commits "implement $FEATURE" --repo=vercel/next.js --limit 10
```

### Debugging "When Did This Break?"
```bash
# Find commits around suspected break time
gh search commits --repo=owner/repo --committer-date="2024-01-15..2024-01-20" --limit 50

# Find commits touching specific area
gh search commits "authentication" --repo=owner/repo --committer-date=">2024-01-01" --limit 30
```

## Issue Search

Dependency triage, debugging, and security monitoring.

### Core Commands
```bash
# Search issues across GitHub
gh search issues "memory leak" --limit 30

# Search in specific repo with filters
gh search issues "crash on startup" --repo=facebook/react --limit 20
gh search issues --repo=nodejs/node --label=bug --state=open --limit 50

# Find issues with workarounds (highly commented)
gh search issues "timeout" --repo=grpc/grpc-node --sort=comments --limit 15
```

### Dependency Triage
```bash
# Check open bugs in a dependency
gh search issues --repo=axios/axios --label=bug --state=open --sort=created --limit 30

# Find breaking changes discussions
gh search issues "breaking change" --repo=prisma/prisma --state=open --sort=reactions

# Find security-related issues
gh search issues "security" --repo=lodash/lodash --sort=created --limit 20
gh search issues "CVE" --repo=urllib3/urllib3
```

### Incident Response
```bash
# Search for known issue by error message
ERROR_MSG="ENOTFOUND"
gh search issues "$ERROR_MSG" --repo=nodejs/node --sort=reactions --limit 15

# Find workarounds in closed issues
gh search issues "$ERROR_MSG" --repo=nodejs/node --state=closed --sort=comments --limit 10
```

### Severity Assessment
```bash
# High severity: many reactions
gh search issues --repo=vercel/next.js --state=open --sort=reactions --limit 10 --json number,title,reactions,comments | jq '.[] | select(.reactions.total_count > 50)'

# Recent surge (potential problem)
gh search issues --repo=docker/docker-ce --state=open --sort=created --created=">$(date -v-7d +%Y-%m-%d)" --limit 30
```

### Security Monitoring Script
```bash
DEPS=("expressjs/express" "nodejs/node" "axios/axios")
for repo in "${DEPS[@]}"; do
  echo "=== $repo ==="
  gh search issues "security vulnerability" --repo=$repo --state=open --sort=created --limit 5
done
```

## PR Search

Track breaking changes, find migrations, and monitor dependency updates.

### Core Commands
```bash
# Search PRs across GitHub
gh search prs "breaking change" --limit 30

# Search in specific repo
gh search prs "migration" --repo=prisma/prisma --state=merged --limit 20

# Find semver-major PRs
gh search prs --repo=nodejs/node --label=semver-major --state=merged --sort=created --limit 30
```

### Pre-Upgrade Assessment
```bash
REPO="prisma/prisma"
# Find breaking changes
gh search prs "breaking" --repo=$REPO --state=merged --merged=">2023-01-01" --sort=created --limit 30
# Find migration-related PRs
gh search prs "migration" --repo=$REPO --state=merged --limit 20
# Find deprecation notices
gh search prs "deprecated" --repo=$REPO --state=merged --limit 20
```

### Security Patches
```bash
gh search prs "security" --repo=lodash/lodash --label=security --state=merged --limit 20
gh search prs "CVE" --repo=urllib3/urllib3 --state=merged --limit 15
```

### Change Monitoring
```bash
DEPS=("facebook/react" "vercel/next.js" "prisma/prisma")
for repo in "${DEPS[@]}"; do
  echo "=== Recent merges in $repo ==="
  gh search prs --repo=$repo --state=merged --merged=">$(date -v-7d +%Y-%m-%d)" --json number,title --jq '.[].title' | head -10
done
```

## Repo Search

Library discovery, alternative assessment, and dependency evaluation.

### Core Commands
```bash
# Search by keywords
gh search repos "state management react" --limit 20

# Filter by language and quality signals
gh search repos "http client" --language=python --stars=">100" --sort=stars --limit 20

# Find actively maintained projects
gh search repos "graphql client" --updated=">$(date -v-90d +%Y-%m-%d)" --stars=">100"
```

### Library Discovery
```bash
# Step 1: Broad search
gh search repos "csv parser" --language=python --stars=">50" --sort=stars --limit 30
# Step 2: Narrow to maintained
gh search repos "csv parser" --language=python --stars=">50" --updated=">$(date -v-180d +%Y-%m-%d)" --limit 15
# Step 3: Deep dive
gh search repos "csv parser" --language=python --stars=">500" --json fullName,description,stargazersCount,forksCount,openIssuesCount,license,updatedAt
```

### Health Scoring Heuristic

| Signal | Good | Warning | Bad |
|--------|------|---------|-----|
| Last update | < 30 days | 30-180 days | > 180 days |
| Open issues ratio | < 10% of stars | 10-30% | > 30% |
| Forks/Stars ratio | > 10% | 5-10% | < 5% |
| License | MIT, Apache-2.0, BSD | LGPL, MPL | GPL, No license |

### Comparison Data
```bash
REPO="owner/repo"
gh api repos/$REPO --jq '{stars: .stargazers_count, forks: .forks_count, issues: .open_issues_count, updated: .updated_at, license: .license.spdx_id}'
gh api repos/$REPO/contributors --jq 'length'
```

## Search Operators Reference

| Operator | Example | Applies To |
|----------|---------|------------|
| `--language` | `--language=python` | code, repos |
| `--repo` | `--repo=owner/name` | code, commits, issues, prs |
| `--path` | `--path=src` | code |
| `--extension` | `--extension=tsx` | code |
| `--owner` | `--owner=facebook` | code, repos |
| `--state` | `--state=open` | issues, prs |
| `--label` | `--label=bug` | issues, prs |
| `--sort` | `--sort=stars` | all types |
| `--author` | `--author=user` | commits |
| `--stars` | `--stars=">100"` | repos |
| `--updated` | `--updated=">DATE"` | repos |

## Quick Aliases
```bash
gh alias set find-code '!gh search code "$1" --language="$2" --limit 30'
gh alias set find-usage '!gh search code "import.*$1" --language=typescript --limit 30'
gh alias set dep-issues '!gh search issues --repo="$1" --state=open --sort=reactions --limit 20'
gh alias set dep-bugs '!gh search issues --repo="$1" --label=bug --state=open --limit 20'
gh alias set breaking-prs '!gh search prs "breaking" --repo="$1" --state=merged --sort=created --limit 20'
gh alias set find-lib '!gh search repos "$1" --language="$2" --stars=">100" --sort=stars --updated=">2024-01-01" --limit 20'
```

## Verification
- Search returns relevant results for target entity type
- Multiple sources compared for best practices
- Health metrics gathered for dependency candidates
- Security issues flagged and assessed

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tjboudreaux) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

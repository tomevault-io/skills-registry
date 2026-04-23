---
name: devops-implementation
description: Guidelines for implementing CI/CD scripts and validating them locally without deployment. Used by devops-coding-agent (container-level) and devops-integration-agent (monorepo-level). Technology-agnostic. Use when this capability is needed.
metadata:
  author: wtah
---

# DevOps Implementation Skill

Guidance for implementing CI/CD scripts based on specifications from deployment-architect.

**Critical Constraint**: Validate locally ONLY. No deployments. No cloud costs.

---

## Core Principle

**You create and validate scripts; you don't deploy.**

```
Deployment Specifications (.specs/deployment/)
              ↓
     [DevOps Agents]
              ↓
    CI/CD Scripts + Validation
              ↓
         Done - NO deployment, NO cloud costs
```

---

## Agent Roles

| Agent | Scope | Creates |
|-------|-------|---------|
| **devops-coding-agent** | Container-level CI/CD | `{container}/.ci/`, `{container}/Dockerfile`, `{container}/scripts/ci-*.sh` |
| **devops-integration-agent** | Monorepo pipelines | `.github/workflows/`, `scripts/ci/`, `infrastructure/README.md` |

---

## Technology Agnostic

Always read `.constraints/INFRASTRUCTURE.md` first for:
- CI/CD platform (GitHub Actions, GitLab CI, Azure DevOps)
- Container runtime (Docker, Podman)
- Registry configuration (for placeholders, not actual pushing)
- Naming and tagging conventions

---

## Input

### From Deployment Architect
```
.specs/deployment/
├── pipelines/
│   ├── ci.md                # CI standards and stages
│   └── cd.md                # CD patterns (reference only)
├── container-mapping.md     # Container → infrastructure mapping
└── environments/            # Environment configurations
```

### From Constraints
```
.constraints/INFRASTRUCTURE.md    # CI/CD platform, container runtime
.constraints/TECHNOLOGY.md        # Language, test framework
```

---

## Output

### Container-Level (devops-coding-agent)

```
{container}/
├── .ci/
│   ├── build.yml            # Container build workflow
│   ├── test.yml             # Container test workflow
│   └── package.yml          # Container package workflow
├── Dockerfile               # Container image definition
├── docker-compose.yml       # Local development
├── .dockerignore            # Build exclusions
└── scripts/
    ├── ci-build.sh          # Build script
    ├── ci-test.sh           # Test script
    └── ci-package.sh        # Package script
```

### Monorepo-Level (devops-integration-agent)

```
.github/workflows/           # (or equivalent for platform)
├── ci.yml                   # Main CI pipeline
├── cd-dev.yml               # Dev deployment (structure only)
├── cd-staging.yml           # Staging deployment (structure only)
└── cd-prod.yml              # Prod deployment (structure only)

infrastructure/
└── README.md                # Infrastructure documentation

scripts/ci/
├── build-all.sh             # Build all containers
├── test-all.sh              # Test all containers
└── validate-all.sh          # Validate all scripts
```

---

## Validation Requirements

**All validation must be local - NO cloud costs**

### Required Validations

| What | Tool | Command |
|------|------|---------|
| YAML syntax | yamllint | `yamllint *.yml` |
| Dockerfile | hadolint | `hadolint Dockerfile` |
| Shell scripts | shellcheck | `shellcheck scripts/*.sh` |
| GitHub Actions | actionlint | `actionlint .github/workflows/*.yml` |

### Forbidden Actions

| Action | Why |
|--------|-----|
| `docker push` | Cloud/registry costs |
| `terraform apply` | Cloud costs |
| `terraform plan` (real providers) | May incur API costs |
| Any cloud CLI deployment | Cloud costs |
| Configuring real secrets | Security risk |

---

## Key Principles

- Validate syntax and structure only
- Use placeholder secrets: `${{ secrets.PLACEHOLDER_NAME }}`
- No actual deployments during preparation
- All validation runs locally
- Zero cloud costs during this phase

---

## Workflow Patterns

### Reusable Container Workflows

Each container should have reusable workflows:

```yaml
# {container}/.ci/build.yml
name: Build {container}

on:
  workflow_call:
    inputs:
      working_directory:
        required: true
        type: string
```

### Monorepo Orchestration

Main CI calls container workflows:

```yaml
# .github/workflows/ci.yml
jobs:
  build:
    uses: ./${{ matrix.container }}/.ci/build.yml
```

### Placeholder Secrets

```yaml
env:
  DEPLOY_KEY: ${{ secrets.DEPLOY_KEY_PLACEHOLDER }}
  # Configure real secrets in CI/CD platform after validation
```

---

## Quality Checklist

### Container CI/CD (devops-coding-agent)
- [ ] .ci/ directory with build, test, package workflows
- [ ] Scripts pass shellcheck
- [ ] Dockerfile passes hadolint
- [ ] Workflows pass yamllint
- [ ] No actual deployments

### Monorepo Integration (devops-integration-agent)
- [ ] Main CI orchestrates all containers
- [ ] CD pipelines use placeholders
- [ ] Cross-container references valid
- [ ] Documentation updated
- [ ] No actual deployments

### Validation Complete
- [ ] All validations run locally
- [ ] No cloud API calls made
- [ ] No registry pushes
- [ ] No deployments attempted
- [ ] Zero cloud costs incurred

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wtah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

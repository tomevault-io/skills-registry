---
name: smart-agent-selection
description: | Use when this capability is needed.
metadata:
  author: tgoddessana
---

# Smart Agent Selection Logic

This skill provides the intelligence for automatically selecting the right Argonauts team members based on files discovered by the **Explore agent**.

## Core Philosophy

**No hardcoded patterns.** Instead:
1. Receive file list from Explore agent
2. Analyze file characteristics (path, name, extension)
3. Apply semantic rules to select appropriate reviewers
4. Return agent list with confidence scores

## Analysis Dimensions

### 1. Path Analysis

Examine directory structure for domain signals:

```python
def analyze_path(filepath):
    path_lower = filepath.lower()

    # Backend signals
    if any(x in path_lower for x in ['api', 'controller', 'service', 'handler', 'route']):
        return 'backend'

    # Frontend signals
    if any(x in path_lower for x in ['component', 'page', 'view', 'hook', 'store', 'ui']):
        return 'frontend'

    # Infrastructure signals
    if any(x in path_lower for x in ['docker', 'k8s', 'kubernetes', 'deploy', 'terraform', 'ci']):
        return 'infrastructure'

    # Security signals
    if any(x in path_lower for x in ['auth', 'security', 'password', 'token', 'credential']):
        return 'security'

    # Database signals
    if any(x in path_lower for x in ['model', 'entity', 'repository', 'migration', 'schema']):
        return 'database'

    return 'unknown'
```

### 2. Extension Analysis

File extensions provide strong domain hints:

```python
extension_to_domain = {
    # Backend
    '.controller.ts': 'backend',
    '.service.ts': 'backend',
    '.handler.py': 'backend',
    '.api.js': 'backend',

    # Frontend
    '.tsx': 'frontend',
    '.jsx': 'frontend',
    '.vue': 'frontend',
    '.svelte': 'frontend',

    # Infrastructure
    '.dockerfile': 'infrastructure',
    '.yml': 'infrastructure',  # often CI/CD
    '.yaml': 'infrastructure',
    '.tf': 'infrastructure',

    # Database
    '.sql': 'database',
    '.prisma': 'database',
    '.migration.ts': 'database',

    # Tests
    '.test.ts': 'test',
    '.spec.js': 'test',
}
```

### 3. Filename Analysis

Keywords in filenames are strong signals:

```python
filename_keywords = {
    'security': ['auth', 'password', 'token', 'session', 'credential', 'encrypt', 'hash'],
    'backend': ['api', 'controller', 'service', 'handler', 'endpoint', 'route'],
    'frontend': ['component', 'hook', 'page', 'view', 'modal', 'form', 'button'],
    'infrastructure': ['docker', 'deploy', 'kubernetes', 'config', 'nginx', 'apache'],
    'database': ['model', 'entity', 'repository', 'migration', 'schema', 'seed'],
    'test': ['test', 'spec', 'mock', 'fixture']
}
```

## Agent Selection Rules (Post-Explore)

Based on analyzed files, select agents:

### Backend Domain
**Detected when:**
- Files in api/, services/, controllers/ directories
- Files with .controller, .service, .handler extensions
- Filenames contain: api, endpoint, route, handler

**Auto-Select:**
- **Heracles** (PRIMARY) - Backend implementation review
- **Lynceus** (CONDITIONAL) - If any auth/validation detected
- **Atalanta** (CROSS-CUTTING) - Test coverage validation

**Confidence:** High if >3 backend files, Medium if 1-3

### Frontend Domain
**Detected when:**
- Files in components/, pages/, views/, hooks/ directories
- Files with .tsx, .jsx, .vue, .svelte extensions
- Filenames contain: component, page, hook, modal, form

**Auto-Select:**
- **Orpheus** (PRIMARY) - Frontend implementation, UX, performance
- **Calliope** (CONDITIONAL) - If >5 components (naming/docs)
- **Atalanta** (CROSS-CUTTING) - Frontend test coverage

**Confidence:** High if >3 frontend files, Medium if 1-3

---

### Security Domain
**Detected when:**
- Files in auth/, security/ directories
- Filenames contain: auth, password, token, session, credential, encrypt
- Any .env files detected

**Auto-Select:**
- **Lynceus** (PRIMARY) - Vulnerability assessment, threat modeling
- **Heracles** (CONDITIONAL) - If backend implementation exists
- **Jason** (ESCALATE) - If >5 security files (architecture review)

**Confidence:** Always HIGH (security is critical)
**Priority:** P0 - Block if issues found

---

### Infrastructure Domain
**Detected when:**
- Files: Dockerfile, docker-compose.yml, *.tf
- Directories: .github/workflows/, k8s/, terraform/
- Filenames contain: deploy, kubernetes, nginx, apache

**Auto-Select:**
- **Argus** (PRIMARY) - Infrastructure, deployment, operational readiness
- **Lynceus** (MANDATORY) - Infrastructure security
- **Jason** (CONDITIONAL) - If major deployment changes

**Confidence:** High if Dockerfile or K8s manifests found
**Priority:** P0 - Deployment safety critical

---

### Database Domain
**Detected when:**
- Files in models/, entities/, repositories/, migrations/
- Files: schema.prisma, *.sql, *.migration.*
- Filenames contain: model, entity, repository, migration

**Auto-Select:**
- **Heracles** (PRIMARY) - Data modeling, query optimization
- **Lynceus** (MANDATORY) - SQL injection, access control
- **Jason** (CONDITIONAL) - If schema changes (data architecture)

**Confidence:** High if migrations or schema files found
**Priority:** P0 - Data integrity critical

---

### Test Domain
**Detected when:**
- Files: *.test.*, *.spec.*
- Directories: tests/, __tests__/, test/, spec/

**Auto-Select:**
- **Atalanta** (PRIMARY) - Test quality, coverage, edge cases
- **Jason** (CONDITIONAL) - If test architecture files found

**Confidence:** High if in test directory
**Priority:** P2 - Quality important but not blocking

---

### Documentation Domain
**Detected when:**
- Files: README.md, CONTRIBUTING.md, *.md
- Directories: docs/
- Files: openapi.yml, swagger.yml

**Auto-Select:**
- **Calliope** (PRIMARY) - Documentation quality, clarity
- **Jason** (CONDITIONAL) - If architecture docs (ADR, design docs)

**Confidence:** Medium
**Priority:** P3 - Important but lower priority

---

## Composite Selection Logic

### Multiple Domains Detected

When Explore returns files spanning multiple domains, **combine** agent selections:

**Example 1: Backend + Security**
```
Files discovered:
  - src/api/auth.ts (backend + security)
  - src/api/users.ts (backend)
  - src/middleware/auth.middleware.ts (security)

Analysis:
  → Backend domain: 2 files
  → Security domain: 2 files (auth-related)

Selected agents:
  → Heracles (backend implementation)
  → Lynceus (security - auth critical)
  → Atalanta (test coverage)
```

**Example 2: Full-Stack Feature**
```
Files discovered:
  - src/api/payment.ts (backend)
  - src/components/PaymentForm.tsx (frontend)
  - migrations/001_add_payments.sql (database)

Analysis:
  → Backend: 1 file
  → Frontend: 1 file
  → Database: 1 file

Selected agents:
  → Jason (architecture - cross-domain feature)
  → Heracles (backend + database)
  → Orpheus (frontend)
  → Lynceus (payment security critical)
  → Atalanta (test coverage)
```

### Cross-Cutting Concerns (Always Apply)

| Condition | Add Agent | Reason |
|-----------|-----------|--------|
| **>10 files total** | **Jason** | Architecture oversight needed |
| **ANY auth/security files** | **Lynceus** | Security always critical |
| **ANY production code** | **Atalanta** | Test coverage validation |
| **Database schema changes** | **Jason** | Data architecture critical |
| **New external dependencies** | **Jason** | Dependency decisions |

---

## Selection Algorithm (Post-Explore)

```python
def select_agents(files_from_explore):
    """
    Input: List of file paths from Explore agent
    Output: List of agent names with confidence scores
    """
    domains = analyze_files(files_from_explore)
    agents = {}

    # Domain-based selection
    if domains['backend'] > 0:
        agents['heracles'] = {'confidence': 'high', 'reason': f"{domains['backend']} backend files"}

    if domains['frontend'] > 0:
        agents['orpheus'] = {'confidence': 'high', 'reason': f"{domains['frontend']} frontend files"}

    if domains['infrastructure'] > 0:
        agents['argus'] = {'confidence': 'high', 'reason': f"{domains['infrastructure']} infra files"}

    # Security (ALWAYS if detected)
    if domains['security'] > 0 or has_auth_keywords(files_from_explore):
        agents['lynceus'] = {'confidence': 'critical', 'reason': 'Security-critical files detected'}

    # Cross-cutting concerns
    if len(files_from_explore) > 10:
        agents['jason'] = {'confidence': 'high', 'reason': 'Architecture oversight for large scope'}

    if has_production_code(files_from_explore):
        agents['atalanta'] = {'confidence': 'medium', 'reason': 'Test coverage validation'}

    if domains['documentation'] > 3:
        agents['calliope'] = {'confidence': 'medium', 'reason': 'Documentation quality check'}

    return agents
```

---

## Confidence Levels

| Confidence | Meaning | Action |
|------------|---------|--------|
| **critical** | Security or data integrity | MUST include agent, P0 priority |
| **high** | Clear domain match (>3 files) | Auto-include, proceed |
| **medium** | Partial match or cross-cutting | Include with note |
| **low** | Ambiguous or edge case | Ask user for confirmation |

---

## Edge Cases

| Situation | Solution |
|-----------|----------|
| **Unknown file types** | Default to Jason (general architecture review) |
| **Too many agents (>5)** | Prioritize by criticality, show user for confirmation |
| **No clear domain match** | Jason + Atalanta (general review + tests) |
| **Only config files** | Argus + Lynceus (config + secrets check) |
| **Only test files** | Atalanta + Jason (test architecture) |
| **Only docs** | Calliope only |

---

## Complete Example

```markdown
Input: Files from Explore agent
  - src/api/users.ts
  - src/api/auth.ts
  - src/api/payment.ts
  - src/services/email.service.ts
  - src/middleware/auth.middleware.ts
  - tests/api/users.test.ts

Step 1: Analyze files
  Backend files: 4 (api/, services/)
  Security files: 2 (auth.ts, auth.middleware.ts, payment.ts)
  Test files: 1 (users.test.ts)
  Total: 6 files

Step 2: Apply selection rules
  → Backend (4 files) → Heracles (confidence: high)
  → Security (2 files, payment) → Lynceus (confidence: critical)
  → Tests present → Atalanta (confidence: medium)

Step 3: Apply cross-cutting rules
  → Total files = 6 (< 10) → Jason NOT added
  → Production code present → Atalanta already included

Step 4: Final selection
  {
    "heracles": {"confidence": "high", "reason": "4 backend API/service files"},
    "lynceus": {"confidence": "critical", "reason": "Auth + payment security critical"},
    "atalanta": {"confidence": "medium", "reason": "Test coverage + backend validation"}
  }

Output: 3 agents selected
  - Heracles (PRIMARY - Backend)
  - Lynceus (CRITICAL - Security)
  - Atalanta (CROSS-CUTTING - QA)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tgoddessana) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

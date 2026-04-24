---
name: documentation
description: Generate documentation using approved templates with proper structure, complete frontmatter, and separation of concerns. Use when creating new docs, updating documentation, or when comprehensive doc generation is needed. Use when this capability is needed.
metadata:
  author: reeinharddd
---

# Documentation Skill - Complete Guide

> **Purpose:** Generate comprehensive documentation using approved templates with proper structure, complete metadata, and strict separation of concerns.

## When to Use This Skill

- Creating new documentation of any type
- Updating existing documentation
- Refactoring docs with violations
- User requests documentation help
- Code changes require documentation updates
- New features need documentation

## MCP Tools to Use

1. **Before creating:** `mcp_payment-syste_search_full_text` - Check if doc exists
2. **For context:** `mcp_payment-syste_query_docs_by_module` - Get related docs
3. **For relations:** `mcp_payment-syste_get_doc_context` - Load doc with relationships

---

## Template Selection Decision Tree

**CRITICAL:** Use this decision tree to select the correct template:

| If documenting...                            | Use Template                     | Location                                   |
| :------------------------------------------- | :------------------------------- | :----------------------------------------- |
| General info, guide, tutorial, overview      | \`00-GENERAL-DOC-TEMPLATE\`      | \`docs/process/**\`, \`docs/technical/**\` |
| Feature/module implementation (full feature) | \`01-FEATURE-DESIGN-TEMPLATE\`   | \`docs/technical/\*\*/features/\`          |
| Architectural decision (why X over Y)        | \`02-ADR-TEMPLATE\`              | \`docs/technical/architecture/adr/\`       |
| Database tables, columns, indexes            | \`03-DATABASE-SCHEMA-TEMPLATE\`  | \`docs/technical/backend/database/\`       |
| API endpoints, DTOs, contracts               | \`04-API-DESIGN-TEMPLATE\`       | \`docs/technical/backend/api/\`            |
| Offline sync, conflict resolution            | \`05-SYNC-STRATEGY-TEMPLATE\`    | \`docs/technical/architecture/\`           |
| User journeys, UI flows, validation flows    | \`06-UX-FLOW-TEMPLATE\`          | \`docs/technical/frontend/ux-flows/\`      |
| Test strategy, coverage, QA approach         | \`07-TESTING-STRATEGY-TEMPLATE\` | \`docs/technical/\*/testing/\`             |
| Deployment procedures, rollback              | \`08-DEPLOYMENT-RUNBOOK\`        | \`docs/technical/infrastructure/\`         |
| Security audit, vulnerabilities, compliance  | \`09-SECURITY-AUDIT-TEMPLATE\`   | \`docs/technical/security/\`               |

---

## Separation of Concerns Matrix (CRITICAL)

**NEVER mix concerns across document types.** This matrix defines what goes where:

| Concern                          | Database   | Feature           | ADR          | UX         | API        |
| :------------------------------- | :--------- | :---------------- | :----------- | :--------- | :--------- |
| **Table definitions**            | ✅ PRIMARY | 🔗 REF            | ❌           | ❌         | ❌         |
| **Column types & constraints**   | ✅ PRIMARY | ❌                | ❌           | ❌         | ❌         |
| **Indexes & performance**        | ✅ PRIMARY | 🔗 Considerations | ❌           | ❌         | ❌         |
| **Foreign keys & relationships** | ✅ PRIMARY | 🔗 REF            | ❌           | ❌         | ❌         |
| **Triggers & DB-level logic**    | ✅ PRIMARY | ❌                | ❌           | ❌         | ❌         |
| **User interaction flows**       | ❌         | 🔗 Overview       | ❌           | ✅ PRIMARY | ❌         |
| **Validation screens**           | ❌         | 🔗 Overview       | ❌           | ✅ PRIMARY | ❌         |
| **UI mockups/wireframes**        | ❌         | 🔗 Overview       | ❌           | ✅ PRIMARY | ❌         |
| **Business logic algorithms**    | ❌         | ✅ PRIMARY        | 🔗 Rationale | ❌         | ❌         |
| **Service layer implementation** | ❌         | ✅ PRIMARY        | ❌           | ❌         | ❌         |
| **API endpoints & DTOs**         | ❌         | 🔗 REF            | ❌           | ❌         | ✅ PRIMARY |
| **Request/response contracts**   | ❌         | 🔗 REF            | ❌           | ❌         | ✅ PRIMARY |
| **Error handling strategies**    | ❌         | ✅ PRIMARY        | 🔗 Rationale | ❌         | ✅ PRIMARY |
| **Offline sync strategy**        | ❌         | 🔗 Overview       | ✅ PRIMARY   | ❌         | 🔗 Impact  |
| **Conflict resolution rules**    | ❌         | 🔗 Overview       | ✅ PRIMARY   | ❌         | ❌         |
| **Technology choice rationale**  | ❌         | ❌                | ✅ PRIMARY   | ❌         | ❌         |

**Legend:**

- ✅ PRIMARY: This is the authoritative source
- 🔗 REF: Brief mention with link to primary source
- ❌ Does NOT belong here

---

## Complete YAML Frontmatter Requirements

**CRITICAL:** Every document MUST have complete YAML frontmatter at the very beginning.

### Universal Required Fields (All Templates)

## \`\`\`yaml

document_type: "[exact-type]" # REQUIRED: Must match template type
module: "[module-name]" # REQUIRED: e.g., "inventory", "authentication"
status: "[status]" # REQUIRED: draft | in-review | approved | deprecated
version: "1.0.0" # REQUIRED: Semantic versioning (Major.Minor.Patch)
last_updated: "YYYY-MM-DD" # REQUIRED: ISO date format
author: "@username" # REQUIRED: GitHub username or agent persona

# Keywords for semantic search (5-10 keywords)

keywords:

- "[keyword-1]" # REQUIRED: At least 5 relevant keywords
- "[keyword-2]"
- "[keyword-3]"
- "[keyword-4]"
- "[keyword-5]"

# Related documentation (cross-references)

related_docs:
database_schema: "" # Path or empty if not applicable
api_design: ""
ux_flow: ""
feature_design: ""
adr: ""
sync_strategy: ""

---

\`\`\`

### Document-Specific Metadata by Type

**Database Schema** (\`document_type: "database-schema"\`):
\`\`\`yaml
database:
engine: "PostgreSQL"
prisma_version: "5.0+"
rls_enabled: true

schema_stats:
total_tables: 5
total_indexes: 10
total_constraints: 8
estimated_rows: "10K-100K"
\`\`\`

**API Design** (\`document_type: "api-design"\`):
\`\`\`yaml
api_metadata:
base_path: "/api/v1/inventory"
auth_required: true
rate_limit: "100 req/min"
versioning: "URI versioning"
\`\`\`

**ADR** (\`document_type: "adr"\`):
\`\`\`yaml
adr_metadata:
adr_number: 1
decision_date: "YYYY-MM-DD"
impact_level: "high"
affected_modules: ["module1", "module2"]
stakeholders: ["@role1", "@role2"]
\`\`\`

**UX Flow** (\`document_type: "ux-flow"\`):
\`\`\`yaml
ux_metadata:
platform: "web" # web | mobile | both
user_roles: ["merchant", "admin"]
complexity: "medium"
\`\`\`

**Sync Strategy** (\`document_type: "sync-strategy"\`):
\`\`\`yaml
sync_metadata:
sync_type: "bidirectional"
conflict_strategy: "last-write-wins"
offline_duration: "7 days"
consistency_model: "eventual"
\`\`\`

---

## Template-Specific Mandatory Sections

### Database Schema Documents

**Mandatory:**

1. Executive Summary (purpose, entity count, dependencies)
2. ER Diagram (PlantUML)
3. Entity Definitions (one per table with schema.TableName)
4. Data Integrity Constraints
5. Performance & Indexing Strategy
6. Migration Strategy
7. Change Log

**Strictly FORBIDDEN:**

- ❌ NO UI/UX flows → Move to \`docs/technical/frontend/ux-flows/\`
- ❌ NO Business logic algorithms → Move to Feature Design
- ❌ NO API endpoint definitions → Move to API Design
- ❌ NO Sync strategies → Move to ADR or Sync Strategy
- ❌ NO User stories → Move to Feature Design

### Feature Design Documents

**Mandatory:**

1. Overview (what/why)
2. User Stories/Requirements
3. Technical Architecture (with links to DB, API, UX docs)
4. Implementation Plan (checklist)
5. Open Questions/Risks
6. Change Log

**Cross-References:**

- Database changes → Link to schema doc
- API contracts → Link to API doc
- UX flows → Link to UX flow doc

### ADR Documents

**Mandatory:**

1. Status (Proposed | Accepted | Deprecated | Superseded)
2. Context (problem statement)
3. Decision (what we chose)
4. Consequences (trade-offs)
5. Alternatives Considered (with pros/cons)
6. Change Log

---

## PlantUML Standards

Use PlantUML for ALL diagrams.

### ER Diagrams (Database)

\`\`\`plantuml
@startuml
!theme plain
entity "Product" as product {

- ## id : UUID <<PK>>
- businessId : UUID <<FK>>
  name : VARCHAR(255)
  sku : VARCHAR(100)
  }
  @enduml
  \`\`\`

### Sequence Diagrams (Flows)

\`\`\`plantuml
@startuml
!theme plain
actor User
participant App
participant Server
User -> App : Scan barcode
App -> Server : GET /products/by-barcode
Server --> App : Product data
@enduml
\`\`\`

### Architecture Diagrams

\`\`\`plantuml
@startuml
!theme plain
node "Client" as C
node "API" as A
database "DB" as D
C --> A : HTTPS
A --> D
@enduml
\`\`\`

**Standards:**

- Always use \`!theme plain\`
- Use meaningful entity/node names
- Add legend when needed
- Keep diagrams focused (max 10 entities)

---

## Change Log Requirements

**MANDATORY:** Every document MUST have an Appendix with Change Log.

\`\`\`markdown

## Appendix A: Change Log

| Date       | Version | Author  | Changes                   |
| :--------- | :------ | :------ | :------------------------ |
| YYYY-MM-DD | 1.0.0   | @Author | Initial document creation |

\`\`\`

**Version Bumping Rules:**

- **Major (x.0.0)**: Breaking changes (schema rename, column removal)
- **Minor (1.x.0)**: Additive changes (new table, new section)
- **Patch (1.0.x)**: Documentation fixes, clarifications

---

## Documentation Creation Workflow

### Step 1: Identify Document Type

Use the Decision Tree above.

### Step 2: Check if Document Exists

Use MCP: \`mcp_payment-syste_search_full_text query="[topic]"\`

### Step 3: Fill YAML Frontmatter (COMPLETE)

- All required fields
- 5-10 relevant keywords
- Related docs paths
- Document-specific metadata

### Step 4: Fill Mandatory Sections

- Executive Summary
- Agent Directives
- All template sections
- Initial change log entry

### Step 5: Validate Separation of Concerns

Check matrix - ensure no violations.

### Step 6: Commit

\`git commit -m "docs(module): add [type] documentation"\`

---

## Common Violations & How to Fix

### Violation: UI Flow in Database Doc

**❌ WRONG:**
\`\`\`markdown

## 5. Product Scanning Flow

When user scans barcode...
\`\`\`

**✅ CORRECT:**
\`\`\`markdown

## 5. Data Integrity Constraints

[... constraints only ...]

**Related:** [Barcode Scanning](../../frontend/ux-flows/SCANNING.md)
\`\`\`

---

## Document Review Checklist

### Metadata

- [ ] YAML at top
- [ ] \`document_type\` matches template
- [ ] \`status\` valid
- [ ] \`version\` semantic
- [ ] \`keywords\` 5-10 terms
- [ ] Document-specific metadata complete

### Structure

- [ ] Correct template
- [ ] All mandatory sections
- [ ] Change log updated

### Separation of Concerns

- [ ] No UI in DB docs
- [ ] No DB in UX docs
- [ ] Appropriate cross-references

---

## Key Reminders

1. **Template decision tree first**
2. **NEVER mix concerns**
3. **Complete YAML frontmatter**
4. **Change log mandatory**
5. **Cross-reference, don't duplicate**
6. **PlantUML for diagrams**
7. **English only**
8. **Validate before commit**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reeinharddd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

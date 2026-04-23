---
name: shesha-skill-creator
description: Creates new Shesha-specific skills by analyzing existing Shesha application code, documentation, and descriptions. Produces properly structured SKILL.md files with supporting reference files following best practices. Use when the user wants to create a new skill for Shesha code generation, workflow patterns, or any Shesha-specific domain by referencing existing implementations.
metadata:
  author: shesha-io
---

# Shesha Skill Creator

Create new skills for Shesha applications by analyzing existing code and documentation provided by the user via $ARGUMENTS.

For general skill authoring best practices, consult [../skill-creator/SKILL.md](../skill-creator/SKILL.md) and its [references/](../skill-creator/references/) directory.

## Workflow

### 1. Gather Inputs

Accept one or more of:
- **Code references**: Paths to existing Shesha project directories or files (e.g. `backend/src/Module/SaGov.Leave.Application/`)
- **Documentation**: URLs or file paths to Shesha-related docs
- **Descriptions**: Natural language description of what the skill should generate

If ambiguous, ask:
- "What artifacts should this skill generate?"
- "Which existing code best represents the patterns to follow?"
- "What would a user say that should trigger this skill?"

### 2. Analyze Referenced Code

Read the referenced code and extract patterns using this Shesha-specific checklist:

#### Base classes and inheritance

- [ ] Application services → `SheshaAppServiceBase`
- [ ] Domain entities → `FullAuditedEntity<Guid>`, `Entity<Guid>`
- [ ] Domain services → `DomainService`
- [ ] AutoMapper profiles → `ShaProfile`
- [ ] Application modules → `SheshaSubModule<TDomainModule>`
- [ ] Domain modules → `SheshaModule`
- [ ] Scheduled jobs → `ScheduledJobBase`
- [ ] Workflow instances → `WorkflowInstanceWithTypedDefinition<T>`
- [ ] Workflow definitions → `WorkflowDefinition`
- [ ] Service tasks → `AsyncServiceTask<TWorkflow>`
- [ ] DB migrations → `Migration`, `OneWayMigration`
- [ ] Other base classes specific to the domain

#### Attributes and decorators

- [ ] `[Entity(TypeShortAlias = "...")]` — type registration
- [ ] `[JoinedProperty("TableName")]` — joined subclass mapping
- [ ] `[DiscriminatorValue("slug")]` — polymorphic storage
- [ ] `[Discriminator]` — TPH inheritance
- [ ] `[ReferenceList("ListName")]` — lookup/enum fields
- [ ] `[Prefix(UsePrefixes = false)]` — column prefix control
- [ ] `[Display(Name, Description)]` — designer metadata
- [ ] `[StringLength(n)]`, `[ReadonlyProperty]`, `[InverseProperty]`
- [ ] `[ScheduledJob]`, `[Migration]`
- [ ] Other domain-specific attributes

#### DI and infrastructure patterns

- [ ] Constructor injection with `IRepository<T, Guid>`
- [ ] `ITransientDependency` marker interface
- [ ] `IocManager.Instance.Resolve<T>()` (static resolution in extension methods)
- [ ] `ISessionProvider` for NHibernate session access
- [ ] `IUnitOfWorkManager` for transaction boundaries
- [ ] `IProcessDomainService` for workflow engine

#### Validation patterns

- [ ] `List<ValidationResult>` + `AbpValidationException` (NOT FluentValidation)
- [ ] `UserFriendlyException` for business rule errors
- [ ] `DynamicDto<T, Guid>` + `MapJObjectToEntityAsync` pattern

#### NHibernate requirements

- [ ] All entity properties are `virtual`
- [ ] Navigation properties vs FK ID properties
- [ ] Session refresh patterns for stale state

#### Folder conventions

- [ ] Domain project: `Domain/{EntityNamePlural}/{Entity}.cs`
- [ ] Application project: `Services/{EntityNamePlural}/`, `Workflows/`, `Jobs/`
- [ ] Migration location: `Migrations/M{timestamp}.cs`
- [ ] Namespace conventions matching folder structure

#### Record for each artifact type found:

- File name pattern
- Location/folder convention
- Full code template (with `{Placeholder}` tokens)
- Required using statements
- Key rules and constraints

### 3. Plan the Skill Structure

Based on analysis, decide:

**Naming**: Use kebab-case describing the domain (e.g. `shesha-leave-management`, `shesha-reporting`).

**Artifact catalog**: List all artifact types the skill will generate, grouped logically.

**File split strategy** (target: SKILL.md under 500 lines):
- SKILL.md: Overview, artifact catalog table, folder structure, quick reference tables
- Supporting files: Full code templates grouped by layer or domain area
- Use `§N` section numbering in supporting files

**Reference the existing skills as structural models:**
- [../shesha-app-layer/SKILL.md](../shesha-app-layer/SKILL.md) — 9 artifact types, 3 supporting files
- [../shesha-workflow/SKILL.md](../shesha-workflow/SKILL.md) — 7 artifact types, 3 supporting files

### 4. Create the Skill

Create `.claude/skills/{skill-name}/` with:

**SKILL.md** — Follow this structure:

```markdown
---
name: {skill-name}
description: {Third-person description of what it generates and when to use it. Include trigger words.}
---

# {Skill Title}

Generate {artifact types} for a Shesha application based on $ARGUMENTS.

## Instructions
- {Key rules specific to this domain}

## Artifact catalog
| # | Artifact | Layer | Template |
|---|----------|-------|----------|
| 1 | {Name} | {Domain/Application} | [{file}.md]({file}.md) §1 |

## Folder structure
{Expected file tree}

## Quick reference
{Base classes, attributes, common patterns tables}

Now generate the requested artifact(s) based on: $ARGUMENTS
```

**Supporting files** — One per logical group, using `§N` sections:

```markdown
# {Group Title}

## §1. {Artifact Name}

**File:** `{FileName}` in `{Location}/`

{Code template with {Placeholder} tokens}

**Key rules:**
- {Constraint 1}
- {Constraint 2}
```

### 5. Verify

- [ ] SKILL.md body under 500 lines
- [ ] Frontmatter has only `name` and `description`
- [ ] `name` matches folder name (lowercase, hyphens only)
- [ ] Description is third person with trigger words
- [ ] All supporting files referenced from SKILL.md artifact catalog
- [ ] References are one level deep (no nested references)
- [ ] Code templates use `{Placeholder}` convention
- [ ] All entity properties marked `virtual` in templates
- [ ] No FluentValidation — uses `List<ValidationResult>` pattern
- [ ] No Windows-style backslash paths
- [ ] No extraneous files (README, CHANGELOG, etc.)

Now create the requested skill based on: $ARGUMENTS

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shesha-io) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

---
name: resourceful
description: Use this skill when designing APIs, reviewing API designs, or generating OpenAPI specs. Trigger on any mention of: API design, REST API, OpenAPI, endpoint design, resource modeling, API spec, Resourceful, Resourceful Document, domain modeling for APIs, or translating domain language into HTTP. Also trigger when a user wants to think through what resources, actions, or relationships an API should have — even if they don't use the word "API" yet.
metadata:
  author: smizell
---

# Resourceful

A language-oriented approach to API design. The process keeps domain thinking separate from HTTP details, capturing the domain model in a structured markdown format before translating it to OpenAPI.

## Design Philosophy

APIs should be designed in the language of the domain first — resources, actions, and relationships — before any HTTP details are introduced. This produces APIs that are intuitive to consumers and grounded in the actual business model.

The process has three loose phases:

1. **Discovery** — Learn the domain through conversation. Capture what emerges in the Resourceful Document.
2. **Review** — Validate the model with the user as it takes shape.
3. **OpenAPI Generation** — Translate the Resourceful Document to an OpenAPI spec mechanically using the mapping rules.

These phases are a rough sequence, not a strict order. Design is iterative — you might generate a spec early to reality-check the model, then revise the Resourceful Document based on what you find. Follow the conversation, not the process.

---

## Phase 1: Discovery

### Listen for use cases, not entities

Start by asking the user what they are trying to do — not what their system contains. The goal is to hear their use cases in plain language, then help them see the resources and actions in what they described.

Good opening: "Walk me through what you're trying to build. What does a user do first?"

Let them describe freely. Do not interrupt with structured questions. Listen for:
- Nouns they keep repeating → likely resources
- Verbs they use → likely actions
- Things they want to see, find, or filter → likely list actions
- State changes they describe → likely custom actions

### Reflect back what you heard

Once they have described their use cases, reflect back what you heard as resources and actions — in their words, not technical terms. Show them the structure you see in their description before writing any Resourceful Document.

Example:

> User: "I want to manage my todos. I start by viewing them. I create a todo. I toggle it complete or incomplete. I archive it. I edit it."
>
> Claude: "From what you described, I see one resource — a **Todo** — with these actions: viewing the list, creating, editing, toggling complete, toggling incomplete, and archiving. Does that sound right? And when you archive a todo — does it disappear from the main list, or stay visible in a separate view?"

Ask targeted questions only where there is genuine ambiguity that affects the model. Do not ask questions you can reasonably infer the answer to.

### Terminology review

Before producing the Resourceful Document, review the terms the user has used:

- **Consistency** — are they using the same word for the same thing throughout? Flag any synonyms (`todo` vs `task`, `complete` vs `done`) and ask them to pick one.
- **Spelling and casing** — note the exact form they use and carry it through the doc consistently (`todo` not `Todo` not `to-do` unless they use that form).
- **Action names** — do the action names reflect what the user actually said? `toggleComplete` and `toggleIncomplete` might be better named `complete` and `uncomplete` depending on how they talked about it.
- **Reuse** — if the same concept appears across multiple resources, make sure it uses the same name everywhere.

This review happens in conversation before writing the doc. Agree on terms with the user first. The Resourceful Document then reflects those agreed terms exactly.

### Produce the Resourceful Document

Once the reflection is confirmed and terms are agreed, produce the Resourceful Document. Read `references/format.md` for the full format specification.

Key rules:
- Every resource lists all actions explicitly — CRUD and custom, no defaults assumed
- Properties are scalars only — structured types go in Relationships
- Actions either target a single resource or the collection
- Actions return the resource, the collection, or nothing — if a different response is needed, it is a new resource
- Nested resources are expressed with `(parent)` on the relationship
- Use the exact terms the user agreed on — no synonyms, no reformatting of their vocabulary
- Prefer many small focused resources over fewer large ones — if a resource has properties that only apply in certain states, or actions that only make sense for some instances, suggest splitting it

### Review with the user

Present the Resourceful Document and walk through it:
- Does this match what you described?
- Are any actions missing or named wrong?
- Are the relationships connecting the right things?
- Are there any terms here that don't look right?

Iterate until the user confirms it is correct.

Every time the Resourceful Document is created or modified, append a changelog entry at the bottom under `# Changelog` with today's date and a bullet for each change. On first creation, the entry is "Initial design".

When a requested change is a breaking change — removing or renaming a property, action, or resource; changing a type or relationship pattern; adding a required property — pause before making it:

1. Flag it as breaking and explain the impact to existing clients
2. Ask for a reason if none was given
3. Suggest a non-breaking alternative where one exists (deprecation, additive change, versioning)
4. If the user confirms, make the change and mark it `[breaking]` in the changelog with their reason

---


## Reviewing an Existing API

When a user brings an existing API (OpenAPI spec, documentation, or description), reverse the process:

1. **Reverse-engineer a Resourceful Document** — Extract resources, actions, and relationships from what exists. Map what you find into Resourceful Document format, making implicit decisions explicit.
2. **Identify gaps and inconsistencies** — Compare the inferred Resourceful Document against the design principles: Are actions named in domain language or HTTP language? Are relationships using the right integration patterns? Is anything missing that the domain implies?
3. **Present findings** — Show the reverse-engineered Resourceful Document alongside specific issues. Frame problems as domain model questions, not HTTP critiques.
4. **Agree on improvements** — Update the Resourceful Document with the user, then regenerate the OpenAPI spec if desired.

Common issues to look for in existing APIs:
- HTTP verbs leaking into action names (`getUser`, `postOrder`)
- Missing resources implied by complex response shapes
- Relationships expressed inconsistently (sometimes embed, sometimes id)
- Actions that return different resource types with no clear domain reason
- Missing or inconsistent error shapes

---

## Phase 2: OpenAPI Generation

Once the Resourceful Document is confirmed, translate it to OpenAPI. Read `references/openapi-mapping.md` for the full mapping rules.

The translation is mechanical — every Resourceful Document element has an unambiguous OpenAPI equivalent. No design decisions should be made during this phase; if something is ambiguous, go back and clarify the Resourceful Document.

Produce a complete OpenAPI 3.1 YAML file.

---

## Output Files

For a complete design engagement, produce two files:

1. `{domain}.resourceful.md` — The Resourceful Document
2. `{domain}.openapi.yaml` — The generated OpenAPI spec

The Resourceful Document is the design artifact — it contains the domain model, conventions, and changelog. The OpenAPI is the technical output. Keep them separate so the domain model can be discussed and evolved independently of the spec.

---

## Maintaining Your API

After the initial Resourceful Document and OpenAPI spec are generated, both files will need to evolve. Here is how to handle changes.

### Edit OpenAPI directly for technical configuration

These changes have no domain equivalent — make them directly in the OpenAPI spec, either by hand or by asking Claude to apply them:

- Security schemes and `security:` declarations
- Server URLs and environments
- Info metadata (contact, license, terms of service)
- External documentation links
- `x-` extension fields for tooling
- Filtering, sorting, and search parameters on specific list operations
- Rate limit headers or other cross-cutting response headers

Example: "Add Bearer token authentication to my spec" — Claude edits the OpenAPI file directly, no Resourceful Document involvement needed.

### Update the Resourceful Document first for domain changes

These changes reflect decisions about the domain model — update the Resourceful Document first, then regenerate or update the affected parts of the spec:

- New resources or types
- New actions on existing resources
- Property additions, removals, or type changes
- Relationship pattern changes (e.g. switching from `id` to `embed`)
- New custom actions

Keeping the Resourceful Document authoritative for domain decisions means it remains a useful design artifact — readable by non-technical stakeholders and independent of OpenAPI's verbosity.

### Asking Claude to edit either file

At any point you can ask Claude to make changes to either file directly — just describe what you want. Claude will determine whether the change is domain-level (update Resourceful Document and reflect in OpenAPI) or technical (update OpenAPI only) and handle it accordingly. If it's unclear, Claude will ask.

---

## Reference Files

- `references/format.md` — Full Resourceful Document format specification with annotated examples. Read this when producing or validating a Resourceful Document.
- `references/openapi-mapping.md` — Translation rules from Resourceful Document to OpenAPI. Read this when generating the OpenAPI spec.

---
> Source: [smizell/resourceful](https://github.com/smizell/resourceful) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->

---
name: add-resource
description: Adds a developer resource to resources.md. Fetches the URL to infer name, description, and category; user can provide only a link (optional short description). Presents a summary for validation before adding. Use when the user wants to add a resource, contribute to the list, or insert an entry by URL. Use when this capability is needed.
metadata:
  author: baptistedri
---

# Add resource to open-resources

## Goal

Add a single developer resource to `resources.md` using the URL as the main input. Fetch the page to determine what the resource is, **present a summary to the user for validation**, then insert only after confirmation.

## Inputs

| Input | Required | Description |
|-------|----------|-------------|
| **URL** | Yes | Link to the resource (https preferred). |
| **Description** | No | Optional short sentence. If provided and it conflicts with what the fetch reveals, ask the user to confirm. |
| **Category** | No | Inferred from the fetched content. If the user specifies one, use it only if it matches the inferred category or ask for confirmation if it differs. |

The user can add a resource by **giving only a link**. A description is optional; if omitted, derive it from the fetched page.

## URL normalization

Before checking duplicates and before writing the row, **normalize** the URL so that the same resource is always stored the same way:

- **Scheme**: use `https://` (if the user gave `http://`, switch to `https://`).
- **Host**: lowercase. Prefer **with `www.`** when the domain is a classic website (e.g. `https://www.reacticx.com` rather than `https://reacticx.com`). For known "apex" domains (e.g. API docs, `vercel.com`, `nextjs.org`), keep without `www.` if that is the canonical form; in doubt, use `www.` for consistency.
- **Path**: lowercase. **Remove trailing slash** unless the path is exactly `/` (e.g. `https://example.com/page/` → `https://www.example.com/page`).
- **Query and fragment**: remove empty `?` or `#`; otherwise keep as-is (lowercase if you normalize them).
- **Result**: one canonical form per resource (e.g. `https://www.thiings.co/things`, `https://nextjs.org`, `https://ui.shadcn.com`).

Apply this normalization to the URL before duplicate check and before inserting the row.

## Duplicate detection

After normalizing the URL:

1. **Read** `resources.md` and collect all URLs in the Link column (strip whitespace, compare in normalized form if needed).
2. **Normalize** each existing URL with the same rules above, then compare with the new URL (case-insensitive host/path after normalization).
3. If the **normalized URL already exists** anywhere in the file, **do not add** the resource. Inform the user that the resource is already in the list and, if useful, in which section it appears.

Also treat as duplicate: same **resource name** already present in the **same section** (even if the URL differs), to avoid two rows for the same product in one category.

## Workflow

1. **Get the URL** from the user (required). Optionally get a description and/or category.
2. **Normalize** the URL (see rules above).
3. **Fetch the URL** (e.g. with web fetch or equivalent). If the fetch fails (timeout, 404, unreachable), **do not add** the resource; inform the user and stop.
4. **From the fetched content**, determine:
   - **Resource name**: e.g. from `<title>`, `og:title`, main heading, or site name. Prefer the official product/site name.
   - **Description**: one short sentence (what it is or what it's for). Use the page's own wording (tagline, meta description, first paragraph) when possible.
   - **Category**: map to one of the existing categories using the list below. If nothing fits, **do not create a category yet**; ask the user to confirm the new category name and that it will be created.
5. **Check duplicates**: read `resources.md`, normalize existing URLs, and compare. If the normalized URL already exists, or the same resource name exists in the same section, stop and inform the user.
6. **If the user provided a description**: compare it with the inferred description. If they are **inconsistent or contradictory**, ask the user to confirm which to use (e.g. "Le site indique X; tu as écrit Y. On garde lequel ?").
7. **If you have any doubt** (ambiguous name, unclear category, conflicting info), **do not add** the resource; ask for confirmation and, if needed, the correct name, description, or category.
8. **Summary for validation**: before writing anything, **present to the user** a short summary:
   - **Name**: (the resource name you will use)
   - **Description**: (the one-line description)
   - **Category**: (existing or proposed new category)
   - **URL**: (normalized URL)
   - **Row as it will appear**: `| Name | Description | URL |`
   Ask explicitly: "Valider cet ajout ?" (or equivalent). **Do not modify** `resources.md` until the user confirms (e.g. "oui", "valide", "go").
9. **If the user validates**: read `resources.md`, insert the new row in the right section (create the section only if it's a new category and already confirmed), sort rows alphabetically by Resource name, update the Table of contents if you added a section, then write the file. If the user declines or asks to change something, do not add; adjust the summary if they give corrections and repeat validation.
10. **New category**: if the resource does not fit any existing category, propose a new category name in the summary and **ask for confirmation** in the same validation step. Only create the section and add the TOC entry after the user has validated the whole summary.

## Existing categories

Use these exact names when the resource clearly fits (case-sensitive):

- **React frameworks** — Next.js, Expo, etc.
- **Headless components** — Base UI, unstyled primitives
- **Designed components** — shadcn/ui, Reacticx, copy-paste UI
- **Icons** — Heroicons, Lucide, Thiings
- **Accessibility** — A11y, color contrast, checklists
- **Design** — Checklists, palettes, wireframes, Relume, UI Colors
- **Data fetching** — TanStack Query, etc.
- **Validation** — Zod, etc.
- **Documentation** — Context7, docs lookup for agents
- **Hosting** — Vercel, deployment
- **Agentic development** — Cursor/agent skills, Skills.sh, UI Skills
- **Learning** — Refactoring Guru, TypeHero, learning platforms

If the resource does not fit any of these, propose a new category name in the summary and **confirm with the user** before creating it.

## Rules and constraints

- **One resource per invocation**: only one URL per run; one row added.
- **Fetch before adding**: never add a resource without having successfully fetched its URL. If fetch fails, stop and report.
- **Normalize URL**: always apply the normalization rules before duplicate check and before writing the row.
- **No duplicates**: same normalized URL anywhere in the file, or same resource name in the same section → do not add; inform the user.
- **No guessing**: if name, description, or category cannot be inferred reliably from the fetch, ask the user instead of inventing.
- **Inconsistency**: if the user's description contradicts the page, ask which to keep.
- **Summary then validation**: always show the summary (name, description, category, URL, row) and **wait for explicit user validation** before modifying `resources.md`.
- **New category**: only create a new section after the user has confirmed it in the validation step.
- **Sorting**: alphabetical by Resource name (first column), case-insensitive. Header row stays first.
- **Stored URL**: use the **normalized** URL in the written row.

## Entry format

Each resource is one table row:

```markdown
| Resource name | Short description. | https://www.example.com/path |
```

- Table header: `| Resource | Description | Link |`.
- New sections use the same header. When creating a new section, add an entry to the Table of contents: `- [CategoryName](#category-name)` (heading in lowercase with spaces as hyphens).
- The Link column must contain the **normalized** URL.

## Workflow summary

1. User provides **URL** (required); optionally description and/or category.
2. **Normalize** the URL.
3. **Fetch** the URL; on failure, abort and report.
4. **Infer** name, description, and category from the page.
5. **Check duplicates** (normalized URL anywhere, or same name in same section); if duplicate, stop and inform.
6. If user gave a description and it **conflicts** with the page → ask which to use.
7. **Any doubt** → do not add; ask for confirmation.
8. **Present summary** (name, description, category, normalized URL, row as it will appear) and ask: **Valider cet ajout ?**
9. **Only if the user validates** → read `resources.md`, insert row (and create section + TOC if new category), sort, write. Otherwise do not modify the file.

Keep the flow simple: fetch once, infer once, summarize once, validate once, write once. No scripts unless the user asks for them.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/baptistedri) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

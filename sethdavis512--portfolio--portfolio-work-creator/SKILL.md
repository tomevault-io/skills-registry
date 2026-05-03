---
name: portfolio-work-creator
description: Guides adding portfolio work items to sethdavis.tech via the CMS. Work items are managed in KeystoneJS admin, not in code. Use when the user wants to add a new work item, project, product, or case study to their portfolio website. Also use when the user mentions "add work", "new portfolio item", "create project page", or wants to showcase something in their portfolio. Use when this capability is needed.
metadata:
  author: sethdavis512
---

# Portfolio Work Creator

Work items are managed entirely through the **KeystoneJS CMS**. No route files, no manual code updates - everything is stored in the database and rendered dynamically.

## Architecture Overview

- **Work items live in the CMS database** (Work model in KeystoneJS)
- **Dynamic routing**: Single `work/:slug` route handles all work items
- **CommandPalette**: Fetches work items from GraphQL at root loader
- **Sitemap**: Generated dynamically from CMS data
- **Pre-rendering**: Fetches work slugs from GraphQL at build time

**No manual file edits needed for:**

- `web/app/routes.ts`
- `web/app/components/CommandPalette.tsx`
- `web/app/routes/sitemap.xml.tsx`
- `web/react-router.config.ts`

## When to Use This Skill

Use when:

- Adding new work items, projects, or products to portfolio
- User says "add to my portfolio", "create a work page", "showcase this project"
- Documenting completed work for the portfolio site
- Creating case studies or project pages

## Quick Start Workflow

### Step 1: Gather Project Information

Ask the user for:

- **Project title** (required)
- **URL slug** (kebab-case, required)
- **Description** (1-2 sentences for /work page card)
- **Tech stack** (JSON array of logo identifiers)
- **Content sections** (about, learned, impact paragraphs)

### Step 2: Access CMS Admin

**Local development:**

```
http://localhost:3000/admin
```

**Production:**

```
https://admin.sethdavis.tech/admin
```

Navigate to **Work** list and click **Create Work**.

### Step 3: Fill in Work Fields

| Field                | Description                                       | Required |
| -------------------- | ------------------------------------------------- | -------- |
| `title`              | Display name                                      | Yes      |
| `slug`               | URL path (kebab-case)                             | Yes      |
| `status`             | DRAFT, PUBLISHED, or ARCHIVED                     | Yes      |
| `sortOrder`          | Display order on /work page (lower = first)       | No       |
| `description`        | Card description for /work page                   | Yes      |
| `cta`                | Button text (default: "See more")                 | No       |
| `about`              | Project overview paragraph                        | No       |
| `learned`            | What you learned paragraph                        | No       |
| `impact`             | Why it matters paragraph                          | No       |
| `techStack`          | JSON array: `["typescript", "react", "tailwind"]` | No       |
| `sourceUrl`          | GitHub/source link                                | No       |
| `demoUrl`            | Live demo link                                    | No       |
| `demoUrlText`        | Custom demo button text                           | No       |
| `purchaseUrl`        | Purchase link (for products)                      | No       |
| `purchaseButtonText` | Purchase button text                              | No       |
| `sidebarTitle`       | Product sidebar heading                           | No       |
| `sidebarType`        | none, product, or demo                            | No       |
| `features`           | JSON array of feature strings                     | No       |

### Step 4: Upload Images

Images are uploaded directly in the CMS via Cloudinary integration:

| Field            | Purpose                                     |
| ---------------- | ------------------------------------------- |
| `heroImage`      | Large hero image at top of work detail page |
| `thumbnailImage` | Card thumbnail on /work page                |

Click the image field and upload directly - Cloudinary handles optimization.

**For gallery images**: Create `WorkImage` entries linked to the Work item.

### Step 5: Save and Test Locally

1. Set `status` to **PUBLISHED**
2. Click **Save changes**
3. Visit `http://localhost:5173/work/<slug>` to preview
4. Check `/work` page for card display

### Step 6: Deploy to Production

After confirming the work item looks correct locally:

```bash
# Push to production database
cd cms && npx tsx sync-to-prod.ts --new-only

# Sync image data (for Cloudinary fields)
cd cms && npx tsx sync-images-to-prod.ts

# CRITICAL: Rebuild web to update pre-rendered pages
cd web && railway up -d
```

**Important**: `railway redeploy` only restarts the container - it does NOT rebuild. Pre-rendered pages like `/work` and `/work/*` fetch data at build time, so you MUST use `railway up` to see changes.

## Content Writing Guidelines

Follow the three-part structure:

1. **About (Project Overview)**: What it is, what it does, how it works (3-5 sentences)
2. **Learned (Knowledge Gained)**: Technical learnings and insights (4-6 sentences)
3. **Impact (Portfolio Value)**: Why it matters, what it showcases (4-5 sentences)

See [content-guide.md](references/content-guide.md) for detailed writing patterns and examples.

## Available Tech Stack Logos

JSON array format: `["typescript", "react", "tailwind"]`

Common logos:
`typescript`, `javascript`, `react`, `react-router`, `tailwind`, `daisy`, `postgres`, `prisma`, `better-auth`, `polar`, `railway`, `cloudinary`, `trigger`, `remotion`

## Validation Checklist

After adding a work item:

- [ ] Status set to PUBLISHED
- [ ] Slug is valid kebab-case
- [ ] Description filled in for /work card
- [ ] Hero image uploaded (appears on detail page)
- [ ] Thumbnail image uploaded (appears on /work cards)
- [ ] Content sections filled (about, learned, impact)
- [ ] Tech stack JSON is valid array
- [ ] Tested locally before syncing
- [ ] Ran `sync-to-prod.ts` to push data
- [ ] Ran `sync-images-to-prod.ts` to sync images
- [ ] Ran `railway up -d` from web directory to rebuild

## Common Issues

**Work item doesn't appear on production after sync**:

- Did you run `railway up -d` from the `web/` directory?
- `railway redeploy` does NOT rebuild - use `railway up`

**Images not showing**:

- Check that images were uploaded in CMS (not just referenced)
- Run `sync-images-to-prod.ts` to sync Cloudinary data
- Rebuild with `railway up -d`

**Work item not in CommandPalette**:

- CommandPalette fetches from CMS dynamically - no manual update needed
- Ensure status is PUBLISHED
- If production, rebuild with `railway up -d`

**Sort order wrong on /work page**:

- Adjust `sortOrder` field (lower numbers appear first)
- Sync and rebuild

## Database Sync Commands

```bash
# Pull latest from production to local
cd cms && npx tsx sync-from-prod.ts --clean

# Preview what would be pushed
cd cms && npx tsx sync-to-prod.ts --dry-run

# Push only new items to production
cd cms && npx tsx sync-to-prod.ts --new-only

# Sync image data
cd cms && npx tsx sync-images-to-prod.ts

# Rebuild web (REQUIRED after data sync)
cd web && railway up -d
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sethdavis512) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

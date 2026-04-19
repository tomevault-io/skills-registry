---
name: dev-blog
description: > Use when this capability is needed.
metadata:
  author: tailored-agentic-units
---

# Dev Blog Workflow

## When This Skill Must Be Used

**ALWAYS invoke this skill when the user's request involves ANY of these:**

- Initializing or setting up a new dev blog
- Calibrating or updating a writing style profile
- Capturing a development entry for a future blog post
- Drafting a blog post from captured entries
- Publishing a draft to the live blog
- Any operation involving the capture/draft/publish pipeline
- Questions about blog configuration, media strategy, or content types

## Commands

| Pattern | Command | Description |
|---------|---------|-------------|
| `/dev-blog init` | Init | Scaffold a new blog with Jekyll, CSS cascade layers, and GitHub Pages deployment |
| `/dev-blog calibrate` | Calibrate | Generate or update writing style profile from writing samples |
| `/dev-blog capture <slug>` | Capture | Stage an entry into a named capture bucket |
| `/dev-blog draft <bucket>` | Draft | Generate a rough draft from captured entries in a bucket |
| `/dev-blog publish <draft>` | Publish | Finalize a draft, release media, and deploy |

## Init Metadata

The `init` command collects the following metadata to scaffold the blog:

| Field | Required | Default | Description |
|-------|----------|---------|-------------|
| Repository name | Yes | — | GitHub repository name, used as baseurl (`/{repo}`) |
| Repository owner | Yes | — | GitHub organization or username |
| Site title | Yes | — | Displayed in nav header and page titles |
| Author name | Yes | — | Footer copyright and HTML meta tags |
| Pages domain | No | `{owner}.github.io` | Custom domain or GitHub Pages default |
| Dark theme | Yes | — | Rouge-compatible theme name for dark mode (see [themes.md](references/themes.md)) |
| Light theme | Yes | — | Rouge-compatible theme name for light mode (see [themes.md](references/themes.md)) |

## Blog Configuration

### Content Types

All types use the same `post` layout. Differentiation is through frontmatter only. Categories are open-ended — new categories can be introduced at draft time and will automatically appear in navigation and category pages.

| Type | Category | Purpose |
|------|----------|---------|
| **Progress** | `progress` | Development progress, new capabilities, strategic direction |
| **Engineering** | `engineering` | Technical architecture and design documents |
| **Announcement** | `announcement` | Brief, focused announcements of specific details |
| **Future** | `future` | Speculative engineering concepts explored for the joy of it |

### Post Frontmatter Schema

```yaml
---
layout: post
title: "Post Title"
date: YYYY-MM-DD HH:MM:SS
tags: [tag-1, tag-2, tag-3]
category: progress | engineering | announcement | future
excerpt: "One-sentence summary for the post listing."
---
```

- `layout` is always `post`
- `date` includes time component for same-day ordering (newest at top)
- `category` is singular (e.g. `progress`, `engineering`, `announcement`, `future`)
- `tags` is a YAML array
- `excerpt` truncated to 30 words on home page

### URL Structure

```
/{baseurl}/{category}/YYYY/MM/DD/{slug}.html
```

### Media Strategy

Git history bloats with binary files. Media is hosted via GitHub Releases:

- **Tag pattern**: `post/{slug}`
- **Download URL**: `https://github.com/{owner}/{repo}/releases/download/post/{slug}/{filename}`
- **Local staging**: `assets/media/` (gitignored) — used during drafting, rewritten to release URLs on publish

## Style Profile

The writing style profile at `.claude/context/style-profile.md` calibrates the voice for draft generation. It is produced by the `calibrate` command and consumed by `draft` and `publish`.

If the style profile does not exist when `draft` or `publish` is invoked, trigger calibration first.

## Commands

- [Init — Blog scaffolding](commands/init.md)
- [Calibrate — Style profile generation](commands/calibrate.md)
- [Capture — Entry staging](commands/capture.md)
- [Draft — Post generation from captures](commands/draft.md)
- [Publish — Finalization and deployment](commands/publish.md)

## References

- [Themes — Rouge theme validation and CSS generation](references/themes.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tailored-agentic-units) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

---
name: folder-navigation
description: > Use when this capability is needed.
metadata:
  author: fangdav
---

# Pocket Space Folder Map

Pocket Space is a SocialFi web app facilitating real-life interactions where people can create clubs for IRL meetings using bonding curve mechanics.

## Top-Level Structure

```
Pocket Space/
├── _Active/          — Current working documents (start here)
├── _Archive/         — Historical documents from previous pivots
├── _Meeting_Notes/   — Chronological team meeting notes (YYYY-MM subfolders)
├── Plugins/          — Plugins and integrations
└── README.md
```

## _Active/ — Where All Current Work Lives

Organized by department:

### Product/
Core product planning and design work.
- `Design/` — User flows, screen mockups, UI inventory
- `General_Research/` — Market analysis, competitive research, bonding curve mechanics
- `Brainstorming/` — Open questions, roadmap, ideation, future features

### Finance/
Financial modeling, projections, and budget assumptions.
- `Assumption_Research/` — Research backing the financial model

### Marketing/
All marketing strategy, channel planning, and go-to-market work.
- `Branding/` — Brand strategy and visual direction
- `Customer_Acquisition/` — Acquisition strategies by channel (Customer_Side, Host_Side, Go_to_Market, Grassroots_Marketing, Influencer_Marketing, Partnerships, Social_Media, Traditional_Ads, Other)
- `Marketing_Budget/` — Budget planning and allocation
- `Marketing_Ops/` — Operational tooling (Analytics, SEO, Technology_Stack)
- `General_Research/` — Marketing-specific research

### Legal/
Legal considerations, compliance, and regulatory research.
- `General_Research/` — Deep-dive legal and regulatory research

### Operations/
Operational assets like API key tracking and avatar management.

## Research Topic Folder Pattern

When research leads to a .docx deliverable, everything lives in a self-contained topic folder:

```
[Department_Folder]/
└── [Topic_of_Research]/                  ← Topic folder
    ├── [Topic_of_Research].docx          ← Final deliverable
    └── Research/                          ← All source .md files
        ├── README.md
        ├── Landscape_Research.md
        ├── Comparison_Matrix.md
        ├── Segment_A/                     ← Deeply nested by segment
        │   ├── Segment_A_Deep_Dive.md
        │   └── Sub_Topic/
        │       └── Sub_Topic_Research.md
        └── Segment_B/
            └── Segment_B_Deep_Dive.md
```

This pattern applies anywhere a .docx is produced. The topic folder goes inside the relevant department folder.

## Routing Guide

| Content Type | Goes In |
|---|---|
| Product specs, wireframes, user flows | `_Active/Product/Design/` |
| Market research, competitor analysis | `_Active/Product/General_Research/` |
| Feature ideas, roadmap items | `_Active/Product/Brainstorming/` |
| Financial models, projections | `_Active/Finance/` |
| Research backing financial assumptions | `_Active/Finance/Assumption_Research/` |
| Brand assets, identity work | `_Active/Marketing/Branding/` |
| Growth strategies, acquisition plans | `_Active/Marketing/Customer_Acquisition/` |
| SEO, analytics, marketing tools | `_Active/Marketing/Marketing_Ops/` |
| Marketing research | `_Active/Marketing/General_Research/` |
| Legal research, compliance docs | `_Active/Legal/General_Research/` |
| API keys, ops assets | `_Active/Operations/` |
| Meeting transcripts/notes | `_Meeting_Notes/YYYY-MM/` |
| Outdated docs from old pivots | `_Archive/` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fangdav) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

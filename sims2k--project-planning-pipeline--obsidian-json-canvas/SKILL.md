---
name: obsidian-json-canvas
description: Create and edit JSON Canvas files (.canvas) with nodes, edges, groups, and connections for visual documentation. Use when working with .canvas files, creating visual canvases, mind maps, flowcharts, UX flows, architecture diagrams, or business canvases in Obsidian. Use when this capability is needed.
metadata:
  author: sims2k
---

# JSON Canvas Skill

This skill enables skills-compatible agents to create and edit valid JSON Canvas files (`.canvas`) used in Obsidian and other applications, including project-specific canvas patterns for UX flows, architecture diagrams, roadmaps, and business strategy canvases.

## Overview

JSON Canvas is an open file format for infinite canvas data. Canvas files use the `.canvas` extension and contain valid JSON following the [JSON Canvas Spec 1.0](https://jsoncanvas.org/spec/1.0/).

## File Structure

A canvas file contains two top-level arrays:

```json
{
  "nodes": [],
  "edges": []
}
```

- `nodes` (optional): Array of node objects
- `edges` (optional): Array of edge objects connecting nodes

---

## Node Types

### Text Nodes

Text nodes contain Markdown content.

```json
{
  "id": "6f0ad84f44ce9c17",
  "type": "text",
  "x": 0,
  "y": 0,
  "width": 400,
  "height": 200,
  "text": "# Hello World\n\nThis is **Markdown** content."
}
```

**IMPORTANT**: Newlines must be `\n` (not `\\n`).

### File Nodes

Reference files within the vault.

```json
{
  "id": "a1b2c3d4e5f67890",
  "type": "file",
  "x": 500,
  "y": 0,
  "width": 400,
  "height": 300,
  "file": "Projects/MyProject/03_Product/PRD.md",
  "subpath": "#MVP Features"
}
```

### Link Nodes

Display external URLs.

```json
{
  "id": "c3d4e5f678901234",
  "type": "link",
  "x": 1000,
  "y": 0,
  "width": 400,
  "height": 200,
  "url": "https://figma.com/file/..."
}
```

### Group Nodes

Visual containers for organizing nodes.

```json
{
  "id": "d4e5f6789012345a",
  "type": "group",
  "x": -50,
  "y": -50,
  "width": 1000,
  "height": 600,
  "label": "Phase 1: Discovery",
  "color": "4"
}
```

---

## Edges

Edges connect nodes with optional labels and colors.

```json
{
  "id": "0123456789abcdef",
  "fromNode": "node1",
  "fromSide": "right",
  "toNode": "node2",
  "toSide": "left",
  "toEnd": "arrow",
  "color": "4",
  "label": "leads to"
}
```

| Attribute | Required | Values |
|-----------|----------|--------|
| `fromSide`, `toSide` | No | `top`, `right`, `bottom`, `left` |
| `fromEnd`, `toEnd` | No | `none`, `arrow` (default: `toEnd: arrow`) |
| `color` | No | `"1"`-`"6"` or hex color |
| `label` | No | Edge label text |

---

## Colors

| Preset | Color |
|--------|-------|
| `"1"` | Red |
| `"2"` | Orange |
| `"3"` | Yellow |
| `"4"` | Green |
| `"5"` | Cyan |
| `"6"` | Purple |

---

## Project Canvas Patterns

### UX Flow Canvas (Stage 04)

Use for user journey mapping with clear stages.

```json
{
  "nodes": [
    {
      "id": "landing",
      "type": "text",
      "x": 0,
      "y": 0,
      "width": 220,
      "height": 140,
      "color": "4",
      "text": "## 1. Landing\n\n- Hero headline\n- Value proposition\n- CTA button\n\n**Goal**: <5s clarity"
    },
    {
      "id": "modal",
      "type": "text",
      "x": 280,
      "y": 0,
      "width": 220,
      "height": 140,
      "color": "5",
      "text": "## 2. Input Modal\n\n- Form fields\n- Privacy note\n- Submit button\n\n**Goal**: Low friction"
    },
    {
      "id": "result",
      "type": "text",
      "x": 560,
      "y": 0,
      "width": 220,
      "height": 140,
      "color": "6",
      "text": "## 3. Result\n\n- Personalized output\n- Aha moment\n- Next action\n\n**Goal**: Delight"
    }
  ],
  "edges": [
    {
      "id": "e1",
      "fromNode": "landing",
      "fromSide": "right",
      "toNode": "modal",
      "toSide": "left",
      "toEnd": "arrow",
      "label": "CTA click"
    },
    {
      "id": "e2",
      "fromNode": "modal",
      "fromSide": "right",
      "toNode": "result",
      "toSide": "left",
      "toEnd": "arrow",
      "label": "Submit"
    }
  ]
}
```

### Architecture Canvas (Stage 05)

Use for system architecture with layer grouping.

```json
{
  "nodes": [
    {
      "id": "frontend-group",
      "type": "group",
      "x": 0,
      "y": 0,
      "width": 350,
      "height": 200,
      "label": "Frontend",
      "color": "5"
    },
    {
      "id": "frontend",
      "type": "text",
      "x": 20,
      "y": 40,
      "width": 310,
      "height": 140,
      "text": "## Frontend\n\n- **Framework**: React + TypeScript\n- **Build**: Vite\n- **Styling**: Tailwind CSS\n- **Deploy**: Static hosting"
    },
    {
      "id": "backend-group",
      "type": "group",
      "x": 400,
      "y": 0,
      "width": 350,
      "height": 200,
      "label": "Backend",
      "color": "4"
    },
    {
      "id": "backend",
      "type": "text",
      "x": 420,
      "y": 40,
      "width": 310,
      "height": 140,
      "text": "## Backend\n\n- **Runtime**: Python/Node\n- **API**: REST/GraphQL\n- **Auth**: JWT\n- **Deploy**: Container"
    },
    {
      "id": "data-group",
      "type": "group",
      "x": 200,
      "y": 250,
      "width": 350,
      "height": 150,
      "label": "Data Layer",
      "color": "6"
    },
    {
      "id": "data",
      "type": "text",
      "x": 220,
      "y": 290,
      "width": 310,
      "height": 90,
      "text": "## Database\n\n- **Primary**: PostgreSQL\n- **Cache**: Redis"
    }
  ],
  "edges": [
    {
      "id": "fe-be",
      "fromNode": "frontend",
      "fromSide": "right",
      "toNode": "backend",
      "toSide": "left",
      "toEnd": "arrow",
      "label": "API calls"
    },
    {
      "id": "be-db",
      "fromNode": "backend",
      "fromSide": "bottom",
      "toNode": "data",
      "toSide": "top",
      "toEnd": "arrow",
      "label": "Queries"
    }
  ]
}
```

### Roadmap Canvas (Stage 00)

Use for phase-based timeline visualization.

```json
{
  "nodes": [
    {
      "id": "phase1",
      "type": "text",
      "x": 0,
      "y": 0,
      "width": 280,
      "height": 180,
      "color": "4",
      "text": "## Phase 1: Discovery\n**2-4 weeks**\n\n- [ ] Market research\n- [ ] Persona profiles\n- [ ] Landing page\n\n**Gate**: 200 signups"
    },
    {
      "id": "phase2",
      "type": "text",
      "x": 320,
      "y": 0,
      "width": 280,
      "height": 180,
      "color": "3",
      "text": "## Phase 2: Validate\n**4-8 weeks**\n\n- [ ] Interactive demo\n- [ ] A/B testing\n- [ ] Email sequence\n\n**Gate**: Engagement"
    },
    {
      "id": "phase3",
      "type": "text",
      "x": 640,
      "y": 0,
      "width": 280,
      "height": 180,
      "color": "5",
      "text": "## Phase 3: Build\n**2-4 months**\n\n- [ ] User accounts\n- [ ] Core features\n- [ ] Payments\n\n**Gate**: Paying users"
    },
    {
      "id": "phase4",
      "type": "text",
      "x": 960,
      "y": 0,
      "width": 280,
      "height": 180,
      "color": "6",
      "text": "## Phase 4: Growth\n**Ongoing**\n\n- [ ] Mobile app\n- [ ] Integrations\n- [ ] Growth loops"
    }
  ],
  "edges": [
    {
      "id": "p1-p2",
      "fromNode": "phase1",
      "fromSide": "right",
      "toNode": "phase2",
      "toSide": "left",
      "toEnd": "arrow"
    },
    {
      "id": "p2-p3",
      "fromNode": "phase2",
      "fromSide": "right",
      "toNode": "phase3",
      "toSide": "left",
      "toEnd": "arrow"
    },
    {
      "id": "p3-p4",
      "fromNode": "phase3",
      "fromSide": "right",
      "toNode": "phase4",
      "toSide": "left",
      "toEnd": "arrow"
    }
  ]
}
```

### Business Model Canvas (Stage 03)

Visual representation of the 9-block business model.

```json
{
  "nodes": [
    {
      "id": "partners",
      "type": "text",
      "x": 0,
      "y": 0,
      "width": 200,
      "height": 250,
      "color": "6",
      "text": "## 🤝 Key Partners\n\n- Partner 1\n- Partner 2"
    },
    {
      "id": "activities",
      "type": "text",
      "x": 220,
      "y": 0,
      "width": 200,
      "height": 120,
      "color": "5",
      "text": "## ⚡ Key Activities\n\n- Activity 1\n- Activity 2"
    },
    {
      "id": "resources",
      "type": "text",
      "x": 220,
      "y": 130,
      "width": 200,
      "height": 120,
      "color": "5",
      "text": "## 🔑 Key Resources\n\n- Resource 1\n- Resource 2"
    },
    {
      "id": "value",
      "type": "text",
      "x": 440,
      "y": 0,
      "width": 200,
      "height": 250,
      "color": "4",
      "text": "## 💎 Value Propositions\n\n**For [segment]:**\n- Benefit 1\n- Benefit 2"
    },
    {
      "id": "relationships",
      "type": "text",
      "x": 660,
      "y": 0,
      "width": 200,
      "height": 120,
      "color": "3",
      "text": "## 💕 Customer Relationships\n\n- Self-service\n- Community"
    },
    {
      "id": "channels",
      "type": "text",
      "x": 660,
      "y": 130,
      "width": 200,
      "height": 120,
      "color": "3",
      "text": "## 🎯 Channels\n\n- Web\n- Mobile\n- API"
    },
    {
      "id": "segments",
      "type": "text",
      "x": 880,
      "y": 0,
      "width": 200,
      "height": 250,
      "color": "2",
      "text": "## 🤗 Customer Segments\n\n**Primary:**\n- Segment 1\n\n**Secondary:**\n- Segment 2"
    },
    {
      "id": "costs",
      "type": "text",
      "x": 0,
      "y": 270,
      "width": 530,
      "height": 100,
      "color": "1",
      "text": "## 💰 Cost Structure\n\n- Infrastructure: $X/mo\n- Development: $X/mo\n- Marketing: $X/mo"
    },
    {
      "id": "revenue",
      "type": "text",
      "x": 550,
      "y": 270,
      "width": 530,
      "height": 100,
      "color": "4",
      "text": "## 💸 Revenue Streams\n\n- Subscription: $X/mo\n- One-time: $X\n- Target MRR: $X"
    }
  ],
  "edges": []
}
```

### Lean Canvas (Stage 03)

One-page business plan for lean startups.

```json
{
  "nodes": [
    {
      "id": "problem",
      "type": "text",
      "x": 0,
      "y": 0,
      "width": 200,
      "height": 200,
      "color": "1",
      "text": "## ❗ Problem\n\n**Top 3:**\n1. Problem 1\n2. Problem 2\n3. Problem 3\n\n**Existing alternatives:**\n- Current solution"
    },
    {
      "id": "solution",
      "type": "text",
      "x": 220,
      "y": 0,
      "width": 200,
      "height": 200,
      "color": "4",
      "text": "## 💡 Solution\n\n**Top 3 features:**\n1. Feature 1\n2. Feature 2\n3. Feature 3"
    },
    {
      "id": "uvp",
      "type": "text",
      "x": 440,
      "y": 0,
      "width": 200,
      "height": 200,
      "color": "5",
      "text": "## 💎 Unique Value\n\n**Clear message:**\n> \"Your tagline here\"\n\n**High-level concept:**\n> X for Y"
    },
    {
      "id": "advantage",
      "type": "text",
      "x": 660,
      "y": 0,
      "width": 200,
      "height": 200,
      "color": "6",
      "text": "## ✨ Unfair Advantage\n\n- Can't be copied\n- Insider info\n- Network effect\n- Unique expertise"
    },
    {
      "id": "segments",
      "type": "text",
      "x": 880,
      "y": 0,
      "width": 200,
      "height": 200,
      "color": "2",
      "text": "## 🤗 Customer Segments\n\n**Target:**\n- Segment 1\n\n**Early adopters:**\n- Who will buy first?"
    },
    {
      "id": "metrics",
      "type": "text",
      "x": 0,
      "y": 220,
      "width": 350,
      "height": 120,
      "color": "3",
      "text": "## 🗝️ Key Metrics\n\n- Acquisition: X\n- Activation: X\n- Retention: X\n- Revenue: X\n- Referral: X"
    },
    {
      "id": "channels",
      "type": "text",
      "x": 370,
      "y": 220,
      "width": 350,
      "height": 120,
      "color": "5",
      "text": "## 🎯 Channels\n\n**Organic:**\n- SEO, Content\n\n**Paid:**\n- Ads, Partnerships"
    },
    {
      "id": "costs",
      "type": "text",
      "x": 0,
      "y": 360,
      "width": 530,
      "height": 100,
      "text": "## 💰 Cost Structure\n\n- Fixed: $X/mo\n- Variable: $X/customer\n- CAC target: $X"
    },
    {
      "id": "revenue",
      "type": "text",
      "x": 550,
      "y": 360,
      "width": 530,
      "height": 100,
      "text": "## 💸 Revenue Streams\n\n- Model: Subscription\n- Pricing: $X/mo\n- LTV target: $X"
    }
  ],
  "edges": []
}
```

### Value Proposition Canvas (Stage 03)

Customer profile ↔ Value map fit visualization.

```json
{
  "nodes": [
    {
      "id": "customer-group",
      "type": "group",
      "x": 400,
      "y": 0,
      "width": 400,
      "height": 400,
      "label": "Customer Profile",
      "color": "2"
    },
    {
      "id": "jobs",
      "type": "text",
      "x": 420,
      "y": 40,
      "width": 180,
      "height": 160,
      "text": "## 🎯 Jobs\n\n**Functional:**\n- Task 1\n- Task 2\n\n**Social:**\n- How perceived\n\n**Emotional:**\n- How feel"
    },
    {
      "id": "pains",
      "type": "text",
      "x": 420,
      "y": 220,
      "width": 180,
      "height": 160,
      "color": "1",
      "text": "## 😣 Pains\n\n1. Pain 1 (5/5)\n2. Pain 2 (4/5)\n3. Pain 3 (3/5)"
    },
    {
      "id": "gains",
      "type": "text",
      "x": 610,
      "y": 40,
      "width": 180,
      "height": 340,
      "color": "4",
      "text": "## 🎁 Gains\n\n**Required:**\n- Must have\n\n**Expected:**\n- Should have\n\n**Desired:**\n- Nice to have\n\n**Unexpected:**\n- Delighters"
    },
    {
      "id": "value-group",
      "type": "group",
      "x": 0,
      "y": 0,
      "width": 350,
      "height": 400,
      "label": "Value Map",
      "color": "5"
    },
    {
      "id": "products",
      "type": "text",
      "x": 20,
      "y": 40,
      "width": 310,
      "height": 100,
      "text": "## 📦 Products & Services\n\n- Product 1\n- Product 2\n- Service 1"
    },
    {
      "id": "pain-relievers",
      "type": "text",
      "x": 20,
      "y": 150,
      "width": 310,
      "height": 110,
      "color": "4",
      "text": "## 💊 Pain Relievers\n\n**Pain 1** → Our solution\n**Pain 2** → Our solution"
    },
    {
      "id": "gain-creators",
      "type": "text",
      "x": 20,
      "y": 270,
      "width": 310,
      "height": 110,
      "color": "4",
      "text": "## 🚀 Gain Creators\n\n**Gain 1** → How we enable\n**Gain 2** → How we enable"
    }
  ],
  "edges": [
    {
      "id": "fit",
      "fromNode": "products",
      "fromSide": "right",
      "toNode": "jobs",
      "toSide": "left",
      "toEnd": "arrow",
      "label": "FIT",
      "color": "4"
    }
  ]
}
```

---

## ID Generation

Node and edge IDs must be unique strings. Use 16-character hex strings:

```
"id": "6f0ad84f44ce9c17"
```

---

## Layout Guidelines

### Positioning

- Coordinates can be negative (canvas extends infinitely)
- `x` increases to the right
- `y` increases downward
- Position refers to top-left corner

### Recommended Sizes

| Node Type | Width | Height |
|-----------|-------|--------|
| Small text | 200-300 | 80-150 |
| Medium text | 300-450 | 150-300 |
| Large text | 400-600 | 300-500 |
| Canvas block | 200-280 | 140-200 |
| Group | Varies | Varies |

### Spacing

- Leave 20-50px padding inside groups
- Space nodes 50-100px apart
- Align to grid (multiples of 20)

---

## References

- [JSON Canvas Spec 1.0](https://jsoncanvas.org/spec/1.0/)
- [JSON Canvas GitHub](https://github.com/obsidianmd/jsoncanvas)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sims2k) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

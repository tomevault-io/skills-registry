---
name: product
description: Use this skill when helping with product management tasks including feature planning, user stories, requirements documentation, PRDs, roadmaps, prioritization, and product decisions for the Pomodoro app.
metadata:
  author: kudosx
---

# Product Role

## Instructions

When working on product-related tasks for this Pomodoro application:

### Working Directory
All product documents MUST be created and stored in the `spec/` folder:
- `spec/prd/` - Product Requirements Documents
- `spec/stories/` - User stories (organized by epic)
- `spec/roadmap/` - Roadmap and planning documents
- `spec/research/` - User research and competitive analysis

### Stories Folder Structure
```
spec/stories/
├── epic-1-timer-management/
│   ├── EPIC.md              # Epic overview & story index
│   ├── US-1-1.md            # US-1.1: Khởi động phiên làm việc
│   ├── US-1-2.md            # US-1.2: Tạm dừng và tiếp tục
│   └── ...
├── epic-2-progress-tracking/
│   ├── EPIC.md
│   ├── US-2-1.md
│   └── ...
└── epic-n-feature-name/
    ├── EPIC.md
    └── US-n-x.md
```

**Naming conventions:**
- Epic folders: `epic-{number}-{feature-name}/` (lowercase, kebab-case)
- Epic overview: `EPIC.md` (uppercase)
- Story files: `US-{epic}-{story}.md` (e.g., `US-1-1.md`, `US-2-3.md`)

### Language
- Write all product documents in **Vietnamese**
- Use English only for technical terms that have no clear Vietnamese equivalent
- Keep file names in English (lowercase, kebab-case)

### Feature Planning
- Consider the existing timer system (work: 25min, shortBreak: 5min, longBreak: 15min)
- Account for the music player with mood-based playlists (peaceful/energetic)
- Respect the vanilla HTML/CSS/JS architecture - no frameworks
- Features should enhance focus and productivity without complexity

### User Stories Format

**EPIC.md template:**
```markdown
# Epic N: Tên Epic (English Name)

**ID:** EPIC-N
**Trạng thái:** ✅ Hoàn thành | 🚧 Đang phát triển | 📋 Planned
**Mô tả:** Mô tả ngắn gọn về epic

## User Stories

| ID | Tên | Trạng thái | File |
|----|-----|------------|------|
| US-N.1 | Tên story | ✅ Done | [US-N-1.md](US-N-1.md) |

## RICE Score Summary
[Table of scores]
```

**Story file template (US-N-X.md):**
```markdown
# US-N.X: Tên Story

**Epic:** [Tên Epic](EPIC.md)
**Trạng thái:** ✅ Done | 🚧 In Progress | 📋 Planned
**Độ ưu tiên:** Critical | High | Medium | Low

---

## Mô tả

As a [user type],
I want [goal/desire],
So that [benefit/value].

## Tiêu chí chấp nhận

- [ ] Specific, testable criterion
- [ ] Another criterion

## Technical Notes
[Implementation details]

## RICE Score
| Reach | Impact | Confidence | Effort | Score |
```

### PRD Structure
1. **Problem Statement** - What user pain point are we solving?
2. **Proposed Solution** - High-level approach
3. **User Stories** - Detailed requirements
4. **Success Metrics** - How we measure impact
5. **Technical Considerations** - Given our static architecture
6. **Out of Scope** - What we're NOT doing

### Prioritization Framework
Use RICE scoring:
- **Reach**: How many users affected?
- **Impact**: How much improvement? (3=massive, 2=high, 1=medium, 0.5=low)
- **Confidence**: How sure are we? (100%, 80%, 50%)
- **Effort**: Person-weeks to implement

Score = (Reach � Impact � Confidence) / Effort

### Key Constraints
- No build process or frameworks
- Data persistence via localStorage only
- Music preferences in static preferences.js file
- Session-only track ratings (not persisted)

## Examples

### Example: Feature Request Analysis
User: "Add dark mode"
Response should include:
- User story with acceptance criteria
- Impact on existing CSS structure
- localStorage key for preference persistence
- Consideration for music player UI

### Example: Prioritization
When comparing features, provide:
- RICE scores for each option
- Recommendation with rationale
- Quick wins vs. strategic investments

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kudosx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

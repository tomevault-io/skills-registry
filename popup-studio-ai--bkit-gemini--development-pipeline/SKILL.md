---
name: development-pipeline
description: | Use when this capability is needed.
metadata:
  author: popup-studio-ai
---

# Development Pipeline

> 9-Phase Development Pipeline for systematic project development

## The 9 Phases

| Phase | Name | Purpose | PDCA |
|-------|------|---------|------|
| 1 | Schema | Define terminology & data structures | Plan |
| 2 | Convention | Set coding rules & standards | Plan |
| 3 | Mockup | Create UI/UX prototypes | Design |
| 4 | API | Design & implement backend API | Do |
| 5 | Design System | Build component library | Do |
| 6 | UI Integration | Connect frontend to backend | Do |
| 7 | SEO/Security | Optimize & secure | Do |
| 8 | Review | Code & architecture review | Check |
| 9 | Deployment | Deploy to production | Act |

## Phase by Level

### Starter (Static Web)
```
Phase 1 → 2 → 3 → 6 → 9
(Skip: 4, 5, 7, 8)
```

### Dynamic (Fullstack)
```
Phase 1 → 2 → 3 → 4 → 6 → 9
(Optional: 5, 7, 8)
```

### Enterprise (Microservices)
```
Phase 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8 → 9
(All required)
```

## Commands

```bash
# Start pipeline
/development-pipeline start

# Check current status
/development-pipeline status

# Move to next phase
/development-pipeline next
```

## Phase Details

### Phase 1: Schema
- Define domain terminology
- Create data models
- Document relationships

### Phase 2: Convention
- Naming conventions
- Code style rules
- Git workflow

### Phase 3: Mockup
- Wireframes
- UI components
- User flows

### Phase 4: API
- Endpoint design
- Request/response schemas
- Authentication

### Phase 5: Design System
- Component library
- Design tokens
- Storybook

### Phase 6: UI Integration
- Frontend implementation
- State management
- API integration

### Phase 7: SEO/Security
- Meta tags
- Security headers
- Vulnerability scan

### Phase 8: Review
- Code review
- Architecture audit
- Performance check

### Phase 9: Deployment
- CI/CD setup
- Environment config
- Go live

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/popup-studio-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

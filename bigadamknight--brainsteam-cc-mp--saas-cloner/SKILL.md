---
name: saas-cloner
description: This skill should be used when the user asks to "clone a SaaS", "build a typeform clone", "replicate calendly", "create an open source alternative to X", or wants to rapidly build functional clones of popular SaaS products focusing on core features. Use when this capability is needed.
metadata:
  author: bigadamknight
---

# SaaS Cloner

Build functional clones of popular SaaS products. Fast. Open source. 100x cheaper.

## The Concept

Most SaaS products have:
- 20% of features that provide 80% of value
- Complex pricing that funds features you don't need
- Vendor lock-in that costs more over time

Clone the 20%. Ship in an hour. Own your data.

## How to Use

### Basic Clone

```
/clone [product name]
```

Example:
```
/clone typeform
```

### Clone with Specifics

```
/clone calendly for internal team scheduling only
```

```
/clone notion but just the wiki/docs features
```

### Clone from URL

```
/clone https://example-saas.com
```

## The Cloning Process

### Phase 1: Analysis (5 minutes)

1. Identify the product's core value proposition
2. List all features visible in UI
3. Categorize: Essential / Nice-to-have / Skip
4. Identify data model requirements
5. Choose tech stack

### Phase 2: Architecture (5 minutes)

1. Design minimal database schema
2. Plan API endpoints
3. Sketch component hierarchy
4. Identify third-party needs (auth, payments, etc.)

### Phase 3: Implementation (30-45 minutes)

1. Set up project structure
2. Implement data models
3. Build API layer
4. Create UI components
5. Wire everything together
6. Add basic styling

### Phase 4: Polish (10 minutes)

1. Add loading states
2. Handle errors gracefully
3. Mobile responsiveness
4. Basic documentation

## Default Tech Stack

| Layer | Technology | Why |
|-------|-----------|-----|
| Frontend | React + TypeScript | Universal, typed |
| Styling | Tailwind CSS | Fast, consistent |
| Backend | Next.js API Routes | Unified stack |
| Database | Supabase (PostgreSQL) | Instant backend |
| Auth | Supabase Auth | Built-in |
| Hosting | Vercel | One-click deploy |

### Alternative Stacks

```
/clone typeform --stack remix+sqlite
/clone calendly --stack vue+firebase
```

## Example: OpenForm (TypeForm Clone)

**Original**: TypeForm - $59/month for basic
**Clone**: OpenForm - Free, self-hosted

### Core Features (cloned)

- Multi-step form builder
- Drag-and-drop questions
- Conditional logic
- Response collection
- Basic analytics
- Embeddable forms

### Skipped Features

- Team collaboration (add if needed)
- Payment integration (add if needed)
- Advanced analytics (add if needed)
- White-labeling (add if needed)

### Result

- Build time: 35 minutes
- Setup time: 15 minutes
- Monthly cost: $0-20 (hosting)
- Data ownership: 100%

## Clone Recipes

### Productivity

| Product | Core Feature | Complexity |
|---------|-------------|------------|
| Typeform | Multi-step forms | Medium |
| Calendly | Scheduling | Medium |
| Notion | Docs/Wiki | High |
| Trello | Kanban boards | Low |
| Todoist | Task lists | Low |

### Marketing

| Product | Core Feature | Complexity |
|---------|-------------|------------|
| Mailchimp | Email campaigns | Medium |
| Buffer | Social scheduling | Medium |
| Hotjar | Session recording | High |
| Intercom | Chat widget | Medium |

### Developer Tools

| Product | Core Feature | Complexity |
|---------|-------------|------------|
| Postman | API testing | Medium |
| Retool | Internal tools | High |
| Linear | Issue tracking | Medium |
| Vercel | Deployments | High |

## Feature Scoping

### Always Include

- Core workflow (the main thing users do)
- Data persistence (save user work)
- Basic auth (protect data)
- Responsive design (mobile works)

### Include If Easy

- Settings/preferences
- Export functionality
- Basic search
- Keyboard shortcuts

### Skip Initially

- Team features
- Advanced permissions
- Integrations
- Analytics beyond basics
- Payment processing

### Add Later Pattern

Leave hooks for future features:
```typescript
// TODO: Add team support
// Schema ready: user.team_id
// API ready: /api/teams (stub)
// UI ready: Team dropdown (hidden)
```

## Deployment Checklist

- [ ] Environment variables documented
- [ ] Database migrations ready
- [ ] One-click deploy button in README
- [ ] Basic README with setup steps
- [ ] License file (MIT recommended)
- [ ] Demo video or screenshots

## Legal Considerations

### Do Clone

- Functionality and workflows
- General UI patterns
- Feature concepts

### Don't Clone

- Brand names or logos
- Exact copy of UI design
- Proprietary algorithms
- Terms of service violations

### Best Practice

Name your clone something original. Don't call it "Free TypeForm" - call it "OpenForm" or "FormFlow".

## Example Session

**User**: `/clone calendly`

**Claude**:
```
Analyzing Calendly...

Core value: Let people book time with you without email back-and-forth

Essential features:
- Calendar connection
- Availability rules
- Booking page
- Email confirmations
- Time zone handling

Nice-to-have (skipping for MVP):
- Team scheduling
- Payment collection
- Custom branding
- Analytics

Tech stack:
- Next.js 14 + TypeScript
- Supabase (auth + database)
- Google Calendar API
- Tailwind CSS
- Vercel deployment

Starting implementation...

[35 minutes later]

✓ BookEasy is ready!

Features:
- Connect Google Calendar
- Set available hours
- Share booking link
- Automatic confirmations
- Time zone detection

Deploy: npx vercel
Demo: http://localhost:3000

Files created:
- /app - Next.js pages
- /components - React components
- /lib - Utilities and API
- /supabase - Database schema
- README.md - Setup instructions
```

## Limitations

- Complex products (Figma, Photoshop) need significant time
- Products with proprietary algorithms can't be exactly replicated
- Some integrations require paid API access
- Real-time collaboration is hard to clone well

## Credits

Inspired by [@dabit3](https://x.com/dabit3/status/2006489676924989860)'s OpenForm project - demonstrating that billion-dollar SaaS products can be cloned to functional MVPs in under an hour using Claude Opus 4.5.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bigadamknight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

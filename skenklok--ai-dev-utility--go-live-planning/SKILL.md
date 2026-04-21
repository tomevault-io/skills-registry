---
name: go-live-planning
description: | Use when this capability is needed.
metadata:
  author: skenklok
---

# Go-Live Planning Skill

You are a DevOps engineer and release manager. Create a comprehensive go-live plan for deploying this application to production.

## Quick Start

When invoked, follow these steps:

1. **Understand the deployment scope** - What needs to go live (mobile apps, web app, backend, database)
2. **Identify soft launch requirements** - Landing page only? Credentials for restricted areas?
3. **Load relevant checklists** - Read from `references/` based on deployment targets
4. **Generate phased plan** - Create timeline with dependencies and owners
5. **Output actionable checklist** - Specific tasks with verification steps

## Tech Stack Context

This skill is configured for:
- **Mobile**: React Native (iOS + Android)
- **Backend**: Node.js/Express on Heroku
- **Database**: PostgreSQL via Prisma on Heroku
- **Auth**: Firebase Authentication
- **Storage**: Cloudflare R2
- **Payments**: Stripe + RevenueCat
- **DNS/CDN**: Cloudflare (domain registrar + DNS + CDN)

## Deployment Components

| Component | Reference |
|-----------|-----------|
| iOS App Store submission | `references/ios-submission.md` |
| Google Play Store submission | `references/android-submission.md` |
| Heroku backend + web app | `references/heroku-deployment.md` |
| Cloudflare DNS + R2 | `references/cloudflare-setup.md` |
| Database migrations | `references/database-go-live.md` |
| Soft launch strategy | `references/soft-launch.md` |

## Soft Launch Strategy

For a soft launch where only the landing page is live:

### Web App Soft Launch Options

1. **Route-based protection** - Landing page public, app routes behind auth/credentials
2. **Feature flags** - Behavioral portal disabled via environment variable
3. **Separate deployments** - Landing page as static site, app as separate Heroku app
4. **Basic auth middleware** - Password protect `/app/*` routes

### Implementation Pattern
```typescript
// Soft launch middleware example
const softLaunchProtection = (req, res, next) => {
  const publicPaths = ['/', '/landing', '/about', '/privacy', '/terms'];
  
  if (publicPaths.some(path => req.path.startsWith(path))) {
    return next(); // Public routes
  }
  
  // Protected routes require credentials or feature flag
  if (process.env.SOFT_LAUNCH_MODE === 'true') {
    // Basic auth or redirect to "coming soon"
    return res.redirect('/coming-soon');
  }
  
  next();
};
```

## Output Format

Generate a go-live plan with the following structure:

### 1. Pre-Launch Checklist

**T-14 Days (2 weeks before)**
- [ ] Task with owner and verification step

**T-7 Days (1 week before)**
- [ ] Task with owner and verification step

**T-1 Day (day before)**
- [ ] Task with owner and verification step

### 2. Launch Day Runbook

| Time | Action | Owner | Verification |
|------|--------|-------|--------------|
| 09:00 | Step 1 | Name | How to verify |

### 3. Post-Launch Checklist

- [ ] Monitoring setup and alerting
- [ ] Smoke tests passed
- [ ] Rollback plan verified

### 4. Rollback Plan

Steps to revert if critical issues found.

## References

For detailed checklists, see:
- `references/ios-submission.md` - App Store Connect setup and submission
- `references/android-submission.md` - Google Play Console setup and submission
- `references/heroku-deployment.md` - Production Heroku configuration
- `references/cloudflare-setup.md` - DNS, SSL, R2, security settings
- `references/database-go-live.md` - PostgreSQL production checklist
- `references/soft-launch.md` - Staged rollout and feature gating

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/skenklok) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

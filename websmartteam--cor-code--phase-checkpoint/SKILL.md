---
name: phase-checkpoint
description: Pause for human verification between major build phases. Use BETWEEN ultraplan phases - after completing one phase, before starting the next. Presents summary of completed work and checklist. Works with ultraplan skill for phased builds. Triggers: checkpoint, verify phase, phase complete, ready to proceed, quality gate, before next phase, review what we built. Use when this capability is needed.
metadata:
  author: websmartteam
---

# Phase Checkpoint

**Purpose**: Pause for human verification before proceeding to next phase. Quality gate.

## When to Use

Call this skill at the end of each major phase:
- After Foundation setup
- After Auth implementation
- After Core features
- After Payments integration
- Before deployment

## Checkpoint Process

### 1. Summary of Completed Work
List what was built this phase:
- Features implemented
- Files created/modified
- Commits made
- Tests passing

### 2. Verification Steps
Guide human through testing on Vercel preview:
```bash
# Push to trigger Vercel preview deployment
git push

# Check the Vercel preview URL (from deployment):
# - https://project-abc123.vercel.app
# - https://project-abc123.vercel.app/auth/login
# - https://project-abc123.vercel.app/dashboard

# ⚠️ NEVER use localhost - always test on real Vercel URLs
```

### 3. Checklist
Present verification checklist:
- [ ] UI renders correctly
- [ ] No console errors
- [ ] Core functionality works
- [ ] Auth flow complete (if applicable)
- [ ] Data persists to database (if applicable)
- [ ] Responsive on mobile

### 4. Decision Point
Ask human:
> **Phase [X] Complete. Ready to proceed to Phase [X+1]?**
>
> Options:
> 1. ✅ Approved - Continue to next phase
> 2. 🔧 Fix issues - [describe what's wrong]
> 3. ⏸️ Pause - Save state, continue later

### 5. State Update
Based on response:
- **Approved**: Update TodoWrite, proceed
- **Fix issues**: Address problems, re-verify
- **Pause**: Update STATE.md with current position

## Example Checkpoint Output

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 PHASE 2 CHECKPOINT: Database & Auth
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✅ Completed:
   • Supabase project connected
   • User profiles table created
   • RLS policies applied
   • Sign up / Sign in / Sign out working
   • Protected dashboard route

🧪 Verify (on Vercel preview URL):
   1. Go to https://[project].vercel.app
   2. Click "Sign Up" - create test account
   3. Check email for confirmation
   4. Confirm and verify redirect to dashboard
   5. Check Supabase → Auth → Users (should see new user)
   6. Click "Sign Out" - verify redirect to home

   ⚠️ Ensure Supabase auth redirects include your preview URL!

📝 Commits:
   • feat: Add Supabase auth configuration
   • feat: Create user profiles schema
   • feat: Implement auth UI components

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Ready to proceed to Phase 3: Core Features?
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Why Checkpoints Matter

From GSD methodology:
> "You can't screw this part up. We have to make sure payments work properly. That's why we're taking so much time here."

Verification prevents:
- Building on broken foundations
- Deploying broken functionality
- Wasting time on features that don't work
- Context rot from debugging old issues

## 🔗 Workflow Integration

**Part of ultraplan workflow**: `ultraplan` creates phases → `phase-checkpoint` verifies each → repeat until complete.

**Related**: `ultraplan` skill (creates the phases), `qa` agent (testing), `design-reviewer` agent (visual verification).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/websmartteam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

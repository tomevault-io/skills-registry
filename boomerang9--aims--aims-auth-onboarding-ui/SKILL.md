---
name: aims-auth-onboarding-ui
description: > Use when this capability is needed.
metadata:
  author: boomerang9
---

# A.I.M.S. Auth & Onboarding UI Skill

This skill standardizes the sign-in, sign-up, and onboarding experience for A.I.M.S., so users always pass through a familiar flow before working with ACHEEVY and Boomer_Angs.

Use with `aims-global-ui`.

## When to Use

Activate when:

- Editing files under `app/(auth)/**`.
- Editing `app/onboarding/**`.
- The user mentions:
  - "Sign in screen"
  - "Registration / onboarding"
  - "Profile setup flow"

---

## Auth Layout

- Full-viewport background using the global A.I.M.S. look.
- Centered glass auth card:
  - Visual width ~360–420px on desktop, full-width minus padding on mobile.
  - Title: "Sign in to A.I.M.S." or equivalent.
  - Short subtext tying to ACHEEVY / Boomer_Angs.
- Social providers (if implemented) as a single row of buttons.
- Email/password form below.

Rules:

- Do not reuse random Apple/Book-of-Vibe layouts.
- All text and controls must be usable on a small phone.

---

## Onboarding Layout

- Multi-step shell with:
  - Stepper (Profile → Goals → LUC estimate → Finish).
  - Main content card with one clear form at a time.
- At Finish:
  - Flag onboarding as complete (as specified by the project).
  - Redirect to Chat w/ ACHEEVY unless user chooses a different landing.

Rules:

- One major decision per step.
- Simple labels (Name, Business focus, Location, etc.).
- No sci‑fi labels like "The Void" in UI text.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/boomerang9) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

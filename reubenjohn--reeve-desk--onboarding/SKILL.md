---
name: onboarding
description: Set up a new Reeve Desk for a user. Use when first installing Reeve or helping someone personalize their instance. Guides through Master Goal definition, preferences, and initial configuration. Use when this capability is needed.
metadata:
  author: reubenjohn
---

# Reeve Onboarding

Welcome to Reeve! This skill helps you personalize your AI Chief of Staff.

## What We'll Configure

1. **Your Identity** - Your name, timezone, communication preferences
2. **Your Master Goal** - The north star that guides all of Reeve's actions
3. **Your Life Pillars** - The 2-4 areas of life you want Reeve to help balance
4. **Initial Preferences** - How you want to receive notifications, when to be disturbed, etc.
5. **First Goals** - 1-3 concrete goals to start tracking

## Step 1: Basic Identity

First, let's personalize your CLAUDE.md. I need to know:

**Required:**
- Your name (how should I address you?)
- Your timezone (for scheduling pulses correctly)

**Optional:**
- Your Desk path (if not using the default)
- Your Reeve Bot installation path

### Actions

After gathering this info, update these files:
1. `CLAUDE.md` - Replace `[USER_NAME]` placeholders with the user's name
2. `.claude/rules/self-awareness.md` - Replace `[YOUR_DESK_PATH]` and `[REEVE_BOT_PATH]` with actual paths

## Step 2: Define Your Master Goal

The Master Goal is Reeve's north star. Everything Reeve does should trace back to this.

**Good Master Goals:**
- "Help me become a more successful person: Career Excellence + Physical/Mental Health + Rich Relationships"
- "Help me balance being a great parent with building my startup"
- "Help me transition careers while maintaining my health and relationships"
- "Help me finish my PhD while not burning out"

**Bad Master Goals:**
- "Help me be more productive" (too vague)
- "Send me reminders" (too tactical)
- "Do what I say" (no direction)

### Pillars

Most Master Goals break down into 2-4 life pillars. Common ones:
- **Career/Work** - Professional growth, projects, learning
- **Health** - Physical fitness, sleep, nutrition, mental health
- **Relationships** - Family, friends, romantic partner, community
- **Personal Growth** - Hobbies, creativity, spirituality, learning

Ask the user to identify their 2-4 pillars and how they should be balanced.

### Actions

Update `CLAUDE.md` with:
1. The Master Goal statement in the Identity section
2. The pillars they've identified
3. Any notes on how to balance them

## Step 3: Communication Preferences

Ask about:
- **Notification style**: Detailed or concise?
- **Morning briefing time**: When do they wake up?
- **Evening wind-down time**: When should daily recap happen?
- **Do Not Disturb**: Any times that should never be interrupted?
- **Emergency contacts**: Who can override DND?
- **Preferred channels**: Telegram, email, other?

### Actions

Create or update `Preferences/Communication.md` with these settings.

## Step 4: Initial Goals

Help the user define 1-3 initial goals for `Goals/Goals.md`.

For each goal:
- **What**: Clear description
- **Why**: How it connects to their Master Goal/pillars
- **Measure**: How will we know progress is being made?
- **Timeframe**: When should this be achieved?

Example:
```markdown
### Goal: Exercise 3-4x per week
**Pillar**: Health
**Why**: More energy for work and family
**Measure**: Track workouts in Diary
**Timeframe**: Establish habit by end of Q1
**Status**: Starting
```

### Actions

Create `Goals/Goals.md` with the user's initial goals.

## Step 5: Responsibilities Setup

Ask about recurring responsibilities:
- Daily routines (exercise, reading, journaling)
- Weekly commitments (team meetings, date nights, meal prep)
- Monthly tasks (reviews, bills, check-ins)

### Actions

Create `Responsibilities/Responsibilities.md` with their recurring items.

## Step 6: Verification

After setup, verify everything works:

1. **Test notification**: Send a test Telegram message
2. **Test pulse scheduling**: Schedule a pulse for 2 minutes from now
3. **Commit initial state**: `git add -A && git commit -m "Initial Reeve setup for [USER_NAME]"`

## Post-Onboarding Checklist

```
□ CLAUDE.md personalized with user's name and Master Goal
□ Pillars defined in CLAUDE.md
□ Self-awareness paths updated in .claude/rules/self-awareness.md
□ Preferences/Communication.md created
□ Goals/Goals.md created with 1-3 initial goals
□ Responsibilities/Responsibilities.md created
□ Test notification sent successfully
□ Test pulse scheduled and will fire
□ Initial state committed to git
```

## Ongoing Onboarding

Reeve learns over time. After initial setup, remind the user:

- "I'll learn your preferences as we interact. If I do something you don't like, just tell me and I'll update my preferences."
- "You can edit any file in this Desk to change how I behave. It's all transparent."
- "Say `/help` anytime to see available commands."

## Example Onboarding Conversation

```
Reeve: "Welcome to Reeve! Let's set me up to be your AI Chief of Staff.

First, what should I call you?"

User: "Alex"

Reeve: "Great, Alex! Now, what's your Master Goal - the overarching thing you want me to help you achieve?

Some examples:
- Balance career growth with health and relationships
- Build my startup while being present for my family
- Finish my degree while maintaining work-life balance"

User: "Help me build my consulting business while staying healthy and being a good partner"

Reeve: "Perfect! So your pillars are:
1. **Career** - Building your consulting business
2. **Health** - Staying physically and mentally fit
3. **Relationships** - Being a good partner

Should I add any others, or does this capture it?"

[continues...]
```

---

**Version**: 1.0
**Purpose**: First-time Reeve setup and personalization

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reubenjohn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

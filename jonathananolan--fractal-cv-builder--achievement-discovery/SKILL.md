---
name: achievement-discovery
description: > Use when this capability is needed.
metadata:
  author: jonathananolan
---

# Achievement Discovery

Build a comprehensive `data-about-career/achievements.md` through structured conversation.
The goal is to extract every quantifiable achievement, skill, and experience the user has -
across all domains of their life - so this file can later feed into CV generation for any role.

## Conversation Flow

### Step 1: Career Goals & Context

Start by understanding the landscape. Ask:

1. What types of roles or industries are you targeting? (Can be multiple or broad)
2. Walk me through your career at a high level - companies, roles, rough dates
3. What do you consider your strongest professional domain?

Do not rush this step. The answers shape which achievements to dig deepest into.

### Step 2: Professional Experience Deep Dive

Start by asking the user to upload their most recent CV if they have it. Parse the CV and Work through each role, most recent first. For each role, systematically extract:

**Context** - What did you actually do day-to-day? What would break if you disappeared?
What did people come to you for?

**Scale** - Users served, requests handled, data volumes, team sizes, budget responsibility.

**Velocity** - What manual processes did you automate? What took hours that now takes
minutes? Build/deploy times you improved?

**Revenue & Cost** - Revenue flowing through your work, costs eliminated, value of
downtime prevented. Estimate collaboratively when exact numbers are unknown.

**Innovation** - Technologies adopted early, hard problems solved, approaches that
failed before yours worked.

**Influence** - Patterns you established that others adopted, people you mentored,
architectural decisions still in place, culture you shaped.

See [question-bank.md](question-bank.md) for the full set of extraction questions
organized by category. Use these when the user gives short or vague answers.

**Collaborative estimation:** When the user says "I don't know the exact numbers,"
help them estimate. Example: "If 1,000 users saved 5 minutes daily, that's 83 hours/day,
roughly 2 full-time employees, approximately $200K/year in saved labor."

### Step 3: Technical Skills Audit

Map capabilities into tiers:

- **Mastery** - Could build production systems confidently. 10K+ hours, live systems running.
- **Proficiency** - Deployed to real users, trusted with critical features.
- **Learning edge** - Last 6 months of active development, working code to show.

Also capture scale credentials: largest user base, highest throughput, most data processed,
biggest team led/influenced.

### Step 4: Education & Certifications

Capture degrees, relevant coursework, honors, study abroad, certifications, publications,
patents. Ask about GPA only if strong. Ask about research or thesis work.

### Step 5: Projects & Open Source

Side projects with real users, open source contributions, conference talks, technical
writing, patents, hackathon wins.

### Step 6: Leadership & Community

Non-work achievements that demonstrate character: sports leadership, volunteering,
mentoring, fundraising, community organizing, board positions. These often differentiate
candidates at senior levels.

### Step 7: Write the Achievements File

Once all information is gathered, compile into `data-about-career/achievements.md` with
the structure in the file, adding additional sections only if necessary:

## Key Behaviors

- Ask one category of questions at a time. Do not overwhelm with 20 questions at once.
- When the user gives a short answer, probe deeper with specific follow-ups from the question bank.
- Always push for numbers. "A lot of users" becomes "approximately 5,000 daily active users."
- Capture everything, even if it seems minor. The cv-builder skill decides what to include later.
- Use professional, direct language. No hype, no cheerleading. See the tone principles in the cv-builder skill's tone-guide.md if needed.
- At the end, read back a summary of key achievements and ask if anything was missed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jonathananolan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

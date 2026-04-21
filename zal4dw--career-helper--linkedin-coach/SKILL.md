---
name: linkedin-coach
description: This skill should be used when the user asks to "review my LinkedIn profile", "optimise my LinkedIn", "write a LinkedIn headline", "build a content strategy", "review my LinkedIn post", or "create a video introduction". Covers full profile audits, headline crafting, content strategy coaching, post review, and video introduction scripts across five modes. Use when this capability is needed.
metadata:
  author: zal4dw
---

# LinkedIn Coach

Comprehensive LinkedIn optimisation across five modes. Choose the one that fits your situation.

## Capabilities

| # | Capability | When to Use |
|:--|:-----------|:------------|
| A | Full Profile Audit | Complete profile review and optimisation |
| B | Content Review | Analyse existing posts for audience alignment |
| C | Content Strategy | Build sustainable 3x/week posting strategy |
| D | Headline Optimisation | Quick headline-only focus |
| E | Video Introduction | 30-second profile video script |

## Quick Start

```
"Review my LinkedIn profile for [target role]"
"Build me a LinkedIn content strategy"
"Review this post before I publish it"
"Rewrite my LinkedIn headline"
"Help me create a LinkedIn video introduction"
```

---

## Accessibility

**At skill start**, check for `career-helper-preferences.md` in the current working directory using the Glob tool. If found, read the YAML frontmatter and apply:

- **dyslexia_friendly: true** → Use short sentences. Number all lists and options (never unnumbered). One decision per message. No idioms or metaphors — use plain replacements. Explicit signposting at every transition ("Step 2 of 4. Next: content strategy."). Refer to saved files by description, not filename. Repeat key details (company names, role titles, dates) — do not assume the user remembers from earlier messages.
- **colour_blind: true** → Never use colour alone to convey meaning. Use labels, text, or icons for all status indicators.

If **no preferences file exists** and this skill was invoked directly (not dispatched by Tim): ask once — "Do you have any accessibility preferences I should know about? For example, if you're dyslexic I can adjust how I format things." If yes, save to `career-helper-preferences.md` using the format documented in the Tim skill before continuing. If the user declines or says no, proceed without creating the file.

These rules apply to **all communication with the user** and to the **formatting of output documents**.

---

## A. Full Profile Audit

**What you need:** LinkedIn profile content (screenshots, copy/paste, or PDF export - see reference for options) + career goals
**Load:** @references/linkedin-profile-review.md
**Template:** @references/linkedin-updates-template.md

Complete profile sections review:
- Photo, banner, headline, about section
- Skills reordering (RSC API top 3)
- Discoverability and recruiter search optimisation
- Activity and content strategy recommendations

**Output:** `applications/{role-slug}/linkedin-profile-review.md`

---

## B. Content Review (Reactive)

**What you need:** Posts to review + target audience
**Load:** @references/linkedin-posts-helper.md

Analyse existing posts:
- Audience alignment assessment
- Decision-maker pain point identification
- Content improvement recommendations

**Output:** `applications/{role-slug}/content-review.md`

---

## C. Content Strategy Coaching (Proactive)

**What you need:** Role, expertise areas, career goals, target audience
**Load:** @references/content-strategy-coaching.md
**Template:** @references/content-calendar-template.md

Build a sustainable posting strategy:
- Discover 3-5 authentic content pillars from real expertise
- 3x/week cadence (Tactical/Strategic/Story mix)
- Build engagement network (20-30 strategic connections in 3 tiers)
- 4-week content calendar with specific topics
- Thread series guidance (when/why to use multi-post sequences)
- Voice coaching - write authentically, not from templates

**Output:** `applications/{role-slug}/content-strategy.md` + `applications/{role-slug}/content-calendar.md`

---

## D. Headline-Only Optimisation

**What you need:** Career goals + target audience
**Load:** @references/linkedin-headline.md

Goal-first headline optimisation:
- Job search, thought leadership, client acquisition, networking, or board/advisory
- Headlines as value statements, not job titles
- Goal-aligned formulas for different structures
- Keyword strategy by target audience
- 3 options with trade-off analysis

**Output:** Headline recommendations in conversation (copy-paste ready)

---

## E. Video Introduction Optimiser

**What you need:** Career goals, target audience, key messages
**Load:** @references/linkedin-video.md

30-second profile video script:
- Hook, Value, Proof, CTA structure
- Goal-specific templates
- Recording and delivery guidance
- Technical setup checklist
- 3 script options with trade-offs

**Output:** Video script in conversation (copy-paste ready)

---

## Application Folder

Role-specific outputs (profile review, content review, content strategy, content calendar) are saved in `applications/{role-slug}/`. When running a capability for a specific role, check if the application folder exists first using Glob. If it doesn't, create it when saving the first output. If the user is not targeting a specific role (e.g., general LinkedIn improvement), save outputs in the workspace root with a descriptive filename.

---

## Persona Adaptation

When the user's context matches a specific persona, load the relevant reference alongside standard capability references:

| Persona | Load Reference | Trigger |
|:--------|:--------------|:--------|
| Career Returner | @references/career-returner-linkedin-guide.md | User mentions career break, returning to work, redundancy, maternity/paternity |
| Early Career | (use career-stage-context.md Early Career section) | User is a graduate, apprentice, or school leaver |
| NED | @references/ned-linkedin-strategy.md | User seeks board roles, NED positions, governor or trustee appointments |
| Fractional | @references/fractional-linkedin-guide.md | User is going fractional, portfolio, or independent consulting |

These references supplement (not replace) the standard capability references. Load both the persona reference and the standard one.

---

## Output Standards

- **UK English** throughout (unless US role explicitly requires US English)
- **No emojis** - Professional tone
- **Cited sources** where applicable
- **Actionable steps** - Concrete next actions, not vague advice

### Tone of Voice
- Address the user as "you", not by name: "Your headline could be stronger" not "Bethan's headline could be stronger" — default to second person for warmth and engagement; occasional name use is fine for emphasis
- Avoid hyperbole and cinema poster phrasing (not "game-changing", "revolutionary", or "supercharge your career")
- Use the **Oxford comma** (serial comma: "skills, experience, and qualifications")
- Never use em dashes. Use commas, semicolons, colons, or full stops instead

### Template Usage

When a capability specifies a template, you MUST:
1. Load the template first using @ symbol
2. Follow the template structure exactly
3. Preserve template footers

---

## Related Skills

After optimising your LinkedIn, you might want:
- **/application-optimiser** - Optimise your CV to match your updated LinkedIn
- **/career-navigator** - Build a networking strategy and 3-month plan
- **/interview-master** - Prepare for interviews

---

*LinkedIn Coach v1.3.0 | Career Helper Plugin | Prosper AI Consulting, UK*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zal4dw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

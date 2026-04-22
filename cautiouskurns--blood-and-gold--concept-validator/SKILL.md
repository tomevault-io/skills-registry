---
name: concept-validator
description: Validate game concepts by analyzing technical feasibility, scope warnings, and comparing to similar games. Use this when the user has a game idea that needs stress-testing. Use when this capability is needed.
metadata:
  author: cautiouskurns
---

# Game Concept Validator Skill

This skill **stress-tests game concepts** by analyzing technical feasibility, identifying scope risks, and providing realistic assessments based on similar games and industry patterns.

---

## When to Use This Skill

Invoke this skill when the user:
- Says "validate this game concept"
- Asks "is this game idea feasible?"
- Presents a game concept and wants reality-check
- Says "can I build [game description]?"
- Asks "what are the risks of this idea?"
- Wants to know "how long would this take?"
- Needs to convince stakeholders (team, investors, etc.)

---

## Core Principle

**Honest, constructive validation** that:
- ✅ Identifies technical challenges early
- ✅ Provides realistic scope estimates
- ✅ Compares to proven similar games
- ✅ Highlights both strengths and risks
- ✅ Suggests scope adjustments if needed
- ✅ Empowers informed decision-making

**This is NOT a "yes/no" tool** - it's a reality check that helps users build smarter.

---

## Input Requirements

The skill needs the user to provide:

### Required Information
1. **Game Concept Description**
   - Core gameplay loop
   - Primary mechanics
   - Genre/style
   - Target experience

### Contextual Information (Ask if missing)
2. **Development Context**
   - Timeline (days/weeks/months)
   - Team size and roles
   - Engine/tools being used
   - Experience level

3. **Scope Parameters**
   - Target platform
   - Expected playtime/content
   - Production quality expectations
   - Must-have vs nice-to-have features

---

## Validation Workflow

### Phase 1: Concept Analysis

**Extract and categorize:**

1. **Core Mechanics**
   - What are the primary gameplay mechanics?
   - How many systems need to interact?
   - What's the complexity of each system?

2. **Technical Requirements**
   - Physics/movement systems needed?
   - AI/pathfinding complexity?
   - Networking/multiplayer?
   - Procedural generation?
   - Save/load systems?
   - UI complexity?

3. **Content Requirements**
   - Art assets needed (count and complexity)
   - Audio requirements
   - Level/map design scope
   - Narrative/dialogue volume
   - Animation complexity

4. **Scope Indicators**
   - Number of interconnected systems
   - Content volume (levels, enemies, items)
   - Polish expectations
   - Platform requirements

---

### Phase 2: Feasibility Assessment

**Evaluate each aspect:**

#### Technical Feasibility
- **🟢 Low Risk**: Built-in engine features, proven patterns
- **🟡 Medium Risk**: Custom systems, moderate complexity
- **🔴 High Risk**: Novel tech, complex integration, multiplayer

#### Scope Feasibility
- **Timeline vs Features**: Can stated features fit timeline?
- **Team vs Workload**: Enough people for required disciplines?
- **Experience vs Complexity**: Does team have needed expertise?

#### Common Risk Patterns

**🚨 Red Flags:**
- "Open world" without large team/timeline
- Multiplayer for first-time developers
- "Procedural everything" without PCG experience
- 3D + physics + AI + multiplayer simultaneously
- "Like GTA but..." scope creep
- Vague core loop ("explore and discover")

**⚠️ Yellow Flags:**
- Many systems with unclear integration
- Ambitious AI without prototyping
- Complex UI/UX without designer
- Cross-platform without testing resources
- Content-heavy with small team

**✅ Green Flags:**
- Clear, focused core loop
- Proven mechanics in new combination
- Incremental feature set (MVP → polish)
- Technical scope matches team skills
- Realistic content volume

---

### Phase 3: Similar Games Analysis

**Find and analyze comparable games:**

1. **Identify Similar Games** (3-5 examples)
   - Same genre/mechanics
   - Similar scope
   - Both indie and AAA examples
   - Successful and failed projects

2. **Extract Lessons**
   - Development timeline
   - Team size
   - What worked well
   - What they cut/simplified
   - Common player feedback

3. **Scope Comparison**
   - How does user's concept compare?
   - What did similar games ship with?
   - What features came later?

---

### Phase 4: Generate Validation Report

**Create comprehensive analysis covering:**

1. **Executive Summary**
   - Overall feasibility rating
   - Key risks
   - Recommended path forward

2. **Technical Analysis**
   - System-by-system breakdown
   - Complexity ratings
   - Required expertise
   - Tech stack recommendations

3. **Scope Analysis**
   - Feature count vs timeline
   - MVP vs full vision
   - Suggested cuts/phases
   - Realistic estimates

4. **Similar Games Comparison**
   - Comparable projects
   - Development stats
   - Key learnings
   - What to emulate/avoid

5. **Risk Matrix**
   - High/medium/low risk items
   - Mitigation strategies
   - Critical path features

6. **Recommendations**
   - Proceed as-is / adjust scope / reconsider
   - Specific cuts or additions
   - Phasing strategy
   - Prototype priorities

---

## Output Format

```markdown
# Game Concept Validation Report

**Concept:** [Concept Name/Summary]
**Submitted by:** [User]
**Date:** [Date]
**Validation Status:** 🟢 Feasible | 🟡 Feasible with Adjustments | 🔴 High Risk

---

## Executive Summary

**Overall Assessment:** [2-3 sentence verdict]

**Feasibility Rating:** [X/10]

**Key Strengths:**
- ✅ [Strength 1]
- ✅ [Strength 2]
- ✅ [Strength 3]

**Key Risks:**
- 🚨 [Critical risk 1]
- ⚠️ [Medium risk 1]
- ⚠️ [Medium risk 2]

**Recommended Path:**
[Clear recommendation: proceed, adjust scope, or reconsider with specific next steps]

---

## Concept Breakdown

### Stated Vision
[Restate user's concept clearly and objectively]

### Core Gameplay Loop
```
1. [Player action]
2. [System response]
3. [Player decision]
4. [Outcome/reward]
→ Repeat
```

### Primary Mechanics
- **[Mechanic 1]**: [Description + complexity rating]
- **[Mechanic 2]**: [Description + complexity rating]
- **[Mechanic 3]**: [Description + complexity rating]

### Development Context
- **Timeline:** [X weeks/months]
- **Team:** [Size and composition]
- **Engine:** [Engine name]
- **Platform:** [Target platform]
- **Experience:** [Team experience level]

---

## Technical Feasibility Analysis

### System-by-System Breakdown

#### [System 1 - e.g., Movement System]
**Complexity:** 🟢 Low | 🟡 Medium | 🔴 High
**Description:** [What it does]
**Requirements:**
- [Technical requirement 1]
- [Technical requirement 2]
**Estimated Effort:** [X days/weeks]
**Risk Level:** [Low/Medium/High]
**Mitigation:** [How to reduce risk]

#### [System 2 - e.g., Combat System]
[Same structure]

#### [System 3 - e.g., Progression System]
[Same structure]

### Technical Risk Summary
| System | Complexity | Risk | Effort | Notes |
|--------|------------|------|--------|-------|
| [System] | 🟢🟡🔴 | Low/Med/High | X days | [Key challenge] |

### Critical Technical Challenges
1. **[Challenge 1]**: [Description + suggested approach]
2. **[Challenge 2]**: [Description + suggested approach]

### Required Expertise
- ✅ **Have:** [Skills team already has]
- ⚠️ **Need:** [Skills to acquire or hire]
- 🔴 **Critical Gap:** [Missing skills that block progress]

---

## Scope Feasibility Analysis

### Timeline vs Features

**Given Timeline:** [X weeks/months]

**Feature Count:** [Y features]

**Realistic Estimate:** [Z weeks/months]

**Scope Verdict:**
- 🟢 **Achievable:** Features fit timeline comfortably
- 🟡 **Tight:** Achievable if everything goes well
- 🔴 **Overscoped:** Need to cut features or extend timeline

### MVP Definition

**Minimum Viable Product (Core Experience):**
1. [Essential feature 1]
2. [Essential feature 2]
3. [Essential feature 3]
4. [Essential feature 4]

**Estimated MVP Timeline:** [X weeks]

**Post-MVP Polish & Content:**
- [Feature to add after MVP]
- [Content expansion]
- [Quality improvements]

**Estimated Full Scope Timeline:** [Y weeks]

### Recommended Cuts (If Overscoped)
- ❌ **Cut:** [Feature] - [Why not essential to core loop]
- ⬇️ **Simplify:** [Feature] - [How to reduce scope]
- 🔄 **Defer:** [Feature] - [Add in post-launch update]

### Content Volume Assessment

| Content Type | Planned | Realistic | Effort | Notes |
|--------------|---------|-----------|--------|-------|
| Levels/Maps | [X] | [Y] | [Z days] | [Comment] |
| Enemies | [X] | [Y] | [Z days] | [Comment] |
| Weapons/Items | [X] | [Y] | [Z days] | [Comment] |
| Art Assets | [X] | [Y] | [Z days] | [Comment] |

---

## Similar Games Comparison

### Game 1: [Title]
**Release:** [Year]
**Team:** [Size]
**Dev Time:** [Months]
**Platform:** [Platform]

**Similarities:**
- [How it's similar to user's concept]

**Key Stats:**
- Development time: [X months]
- Team size: [Y people]
- Budget: [If known]
- Content volume: [Levels, mechanics, etc.]

**What They Did Well:**
- ✅ [Lesson 1]
- ✅ [Lesson 2]

**What They Cut/Simplified:**
- [Feature they initially planned but cut]
- [System they simplified]

**Player Feedback:**
- [What players loved]
- [What players wanted more of]

**Lessons for Your Concept:**
[How this game's experience applies to user's idea]

---

### Game 2: [Title]
[Same structure]

---

### Game 3: [Title]
[Same structure]

---

### Comparative Analysis

**Your Concept vs Similar Games:**

| Aspect | Your Concept | [Game 1] | [Game 2] | [Game 3] |
|--------|--------------|----------|----------|----------|
| Scope | [Rating] | [Rating] | [Rating] | [Rating] |
| Team Size | [X] | [Y] | [Z] | [A] |
| Dev Time | [X mo] | [Y mo] | [Z mo] | [A mo] |
| Complexity | 🟢🟡🔴 | 🟢🟡🔴 | 🟢🟡🔴 | 🟢🟡🔴 |

**Key Insight:**
[What this comparison reveals about feasibility]

---

## Risk Matrix

### 🔴 High Risk Items (Must Address)

| Risk | Impact | Probability | Mitigation Strategy |
|------|--------|-------------|---------------------|
| [Risk 1] | High | High | [How to mitigate] |
| [Risk 2] | High | Medium | [How to mitigate] |

### 🟡 Medium Risk Items (Monitor Closely)

| Risk | Impact | Probability | Mitigation Strategy |
|------|--------|-------------|---------------------|
| [Risk 1] | Medium | High | [How to mitigate] |
| [Risk 2] | High | Low | [How to mitigate] |

### 🟢 Low Risk Items (Manageable)

| Risk | Impact | Probability | Mitigation Strategy |
|------|--------|-------------|---------------------|
| [Risk 1] | Low | Medium | [How to mitigate] |

### Critical Path Analysis

**Blocking Risks** (must solve to proceed):
1. [Risk that blocks all progress]
2. [Foundational risk]

**Parallelizable Risks** (can work on simultaneously):
- [Risk 1]
- [Risk 2]

**Deferrable Risks** (can address later):
- [Risk that affects polish, not core]

---

## Detailed Recommendations

### Verdict: [Proceed / Adjust Scope / Reconsider]

#### If Proceeding As-Is (🟢 Feasible)
**Why it works:**
- [Reason 1]
- [Reason 2]

**To maximize success:**
1. [Recommendation 1]
2. [Recommendation 2]
3. [Recommendation 3]

**Prototype priorities:**
1. [What to build first to validate concept]
2. [Second priority]

---

#### If Adjusting Scope (🟡 Feasible with Changes)
**Required changes:**
1. **[Change 1]**: [Why necessary]
2. **[Change 2]**: [Why necessary]

**Suggested MVP:**
[Redefined minimum viable product with cuts]

**Phased Roadmap:**
- **Phase 1 (MVP):** [Features - X weeks]
- **Phase 2 (Polish):** [Features - Y weeks]
- **Phase 3 (Content):** [Features - Z weeks]

**With these changes:**
- Timeline: [Original X weeks → Adjusted Y weeks]
- Scope: [Original features → MVP features]
- Risk: [Original risk level → New risk level]

---

#### If Reconsidering (🔴 High Risk)
**Why it's risky:**
- [Critical issue 1]
- [Critical issue 2]

**Alternative approaches:**

**Option A: Pivot to Smaller Scope**
[Suggest a narrower version of the concept that's achievable]

**Option B: Prototype First**
[Suggest building a small prototype to validate before committing]

**Option C: Build Prerequisite Skills**
[Suggest smaller projects to build up to this one]

**Questions to ask yourself:**
- Can you live with a much smaller version?
- Do you have 2-3x the stated timeline?
- Can you bring in experienced help?
- Is this the right first project?

---

## Next Steps

### Immediate Actions (This Week)
1. [Actionable step 1]
2. [Actionable step 2]
3. [Actionable step 3]

### Short-Term (Next 2-4 Weeks)
1. [Step 1]
2. [Step 2]

### Long-Term (Beyond Month 1)
1. [Step 1]
2. [Step 2]

### Decision Points
**By [Date]:** [Decision to make - e.g., "Decide if prototype validates core loop"]
**By [Date]:** [Next decision - e.g., "Commit to full production or pivot"]

---

## Appendix: Estimation Formulas

**Used in this report:**

### Feature Complexity Scoring
- **Simple (1x):** Built-in engine features, minimal custom code
- **Moderate (2-3x):** Custom systems, moderate integration
- **Complex (5-10x):** Novel mechanics, deep integration, multiplayer

### Content Volume Estimates
- **Art asset:** [X hours per asset]
- **Level/map:** [Y hours per level]
- **Enemy/character:** [Z hours including AI, art, balance]

### Team Velocity Assumptions
- **Solo developer:** [X hours/week productive time]
- **Small team:** [Y hours/week per person]
- **Experience multiplier:** [Beginner 0.5x, Intermediate 1x, Expert 1.5x]

---

## Follow-Up Services

After reading this report, you can:

- **Refine Concept:** Provide adjusted concept for re-validation
- **Create Prototype Plan:** Use `/vertical-slice-roadmap-planner` for phased roadmap
- **Generate GDD:** Use `/prototype-gdd-generator` to formalize design
- **Explore Alternatives:** Use `/game-concept-generator` for alternative ideas
- **Deep-Dive Analysis:** Request system-specific technical analysis

---

**Report generated by Claude Code - Concept Validator Skill**
**For questions or clarifications, ask for elaboration on any section**
```

---

## Validation Principles

### Be Honest, Not Discouraging
- ✅ Point out risks clearly
- ✅ Suggest concrete solutions
- ✅ Acknowledge what's good
- ❌ Don't crush dreams without alternatives
- ❌ Don't rubber-stamp bad ideas

### Be Specific, Not Vague
- ✅ "Combat system needs state machine, collision detection, hit feedback - estimate 2 weeks"
- ❌ "Combat will be hard"

### Be Realistic, Not Pessimistic
- ✅ Use industry data and similar games
- ✅ Account for learning curves
- ✅ Include buffer for unknowns
- ❌ Don't assume everything goes perfectly
- ❌ Don't assume worst-case for everything

### Be Constructive, Not Critical
- ✅ "Cut feature X, focus on Y" with reasoning
- ✅ "Simplify Z to reduce scope without losing core"
- ❌ "This is too ambitious" without suggestions

---

## Research Strategies

### Finding Similar Games

**Search patterns:**
- "[Genre] + [key mechanic] + indie game"
- "[Core loop description] + game"
- "Games like [inspiration] but smaller scope"

**Sources:**
- Itch.io (indie games with dev blogs)
- Game jams (Ludum Dare, GMTK)
- Postmortems (Gamasutra, dev blogs)
- "Making of" videos (GDC, YouTube)

### Extracting Development Data

**Look for:**
- Dev time (search "development time", "took X months")
- Team size (credits, about pages)
- Tech stack (Unity/Unreal/Godot tags)
- Scope changes (postmortems mention cuts)
- Player counts (Steam stats)

**Red flags in research:**
- No postmortem data available → estimate conservatively
- Only AAA examples → scale down significantly
- Very old games → tech has changed

---

## Complexity Estimation Guide

### System Complexity Ratings

**🟢 Low Complexity** (1-3 days for experienced dev)
- Basic movement (WASD/arrow keys)
- Simple collision detection
- Basic UI (menus, buttons)
- Sprite animation
- Sound effect playback

**🟡 Medium Complexity** (1-2 weeks for experienced dev)
- State machines (player states, enemy AI)
- Pathfinding (A* for navigation)
- Inventory systems
- Save/load functionality
- Particle effects and juice
- Basic procedural generation (e.g., random level layouts)

**🔴 High Complexity** (3+ weeks for experienced dev)
- Multiplayer/networking
- Complex procedural generation (e.g., terrain, dungeons)
- Advanced AI (behavior trees, machine learning)
- Physics-based puzzles
- Dialogue systems with branching
- 3D with custom shaders

### Content Production Rates

**Pixel Art** (solo artist):
- Simple sprite: 1-2 hours
- Character (4 directions, 3 animations): 8-16 hours
- Tileset: 8-16 hours
- UI set: 4-8 hours

**3D Assets** (solo artist):
- Simple prop: 2-4 hours
- Character: 20-40 hours
- Environment: 40-80 hours

**Level Design:**
- Tutorial level: 4-8 hours
- Standard level: 8-16 hours (including layout, testing, balance)

**Audio:**
- Sound effect: 30 mins - 2 hours
- Music track: 4-8 hours

### Multiplier Factors

**Experience level:**
- Beginner: 2-3x estimates
- Intermediate: 1x estimates
- Expert: 0.7x estimates

**Engine familiarity:**
- New engine: 1.5x estimates
- Familiar engine: 1x estimates

**Team coordination:**
- Solo: 1x estimates
- 2-3 people: 1.2x estimates (communication overhead)
- 4+ people: 1.5x estimates (coordination overhead)

---

## Common Concept Patterns

### Pattern: "Like X but Y"

**Example:** "Like Vampire Survivors but with crafting"

**Validation approach:**
1. Research X (Vampire Survivors)
   - What made it successful?
   - How long to develop?
   - What's the minimum feature set?
2. Evaluate Y (crafting addition)
   - How complex is crafting system?
   - Does it complement or compete with core loop?
   - What's the integration cost?
3. Assess combined scope
   - Is it additive (2x effort) or synergistic?

**Common pitfall:** Underestimating integration complexity

---

### Pattern: "Multiple Genres Combined"

**Example:** "Puzzle-platformer with tower defense elements"

**Validation approach:**
1. Identify each genre's core loop
2. Map where loops intersect
3. Assess if loops compete or complement
4. Estimate development for each separately, then add 50% for integration

**Common pitfall:** Each genre needs full implementation, can't cut corners

---

### Pattern: "Procedural Everything"

**Example:** "Roguelike with procedural levels, enemies, weapons, and story"

**Validation approach:**
1. Prioritize what MUST be procedural
2. What can be handcrafted?
3. Prototype generator early (high risk)
4. Assess content creation vs generator development time

**Common pitfall:** Generator takes longer than making content manually

---

### Pattern: "Massive Content Scope"

**Example:** "RPG with 50 quests, 20 enemy types, 100 items"

**Validation approach:**
1. Calculate production time per asset
2. Multiply by quantity
3. Compare to timeline
4. Suggest MVP content volume (usually 20-30% of vision)

**Common pitfall:** Underestimating iteration time (balance, bugs, polish)

---

## Example Validation: "Mech Survivors"

**User Request:** "Validate this concept: A Vampire Survivors-like but with mechs that you customize between runs. 3D graphics, Godot, solo dev, 3 months."

**Validation Output:**

---

# Game Concept Validation Report

**Concept:** Mech Survivors (Vampire Survivors-like with mech customization)
**Submitted by:** User
**Date:** 2025-12-21
**Validation Status:** 🟡 Feasible with Adjustments

---

## Executive Summary

**Overall Assessment:** Achievable concept with strong core but needs scope refinement. 3D + customization significantly increases complexity compared to Vampire Survivors' 2D simplicity. Recommend starting with 2D or simplifying customization.

**Feasibility Rating:** 6/10 (as stated) → 8/10 (with adjustments)

**Key Strengths:**
- ✅ Proven core loop (Vampire Survivors formula)
- ✅ Clear differentiation (mechs + customization)
- ✅ Godot is excellent for this genre

**Key Risks:**
- 🚨 3D art + animation for solo dev in 3 months
- ⚠️ Customization system complexity
- ⚠️ Balance complexity with customizable builds

**Recommended Path:**
Start with **2D top-down** sprites (faster art pipeline) and **simplified customization** (3-4 weapon slots, 10-15 parts total). Build 3D version post-launch if successful.

---

[Rest of detailed report would follow the template above]

---

## Important Notes

- **Tone:** Supportive but honest - help users succeed, don't just say "yes"
- **Data-driven:** Use real examples, not gut feelings
- **Actionable:** Every critique should include solution
- **Scoped:** Focus on feasibility, not full design review
- **Realistic:** Account for Murphy's Law (things take longer than expected)

---

## Example Invocations

User: "Validate this game concept: [description]"
User: "Can I build a [game type] in [timeframe]?"
User: "Is this idea feasible: [concept]"
User: "Reality check on this game idea: [description]"
User: "What are the risks of making [game concept]?"

---

## Workflow Summary

1. **Gather concept details** (ask for missing context if needed)
2. **Analyze technical requirements** (systems, content, complexity)
3. **Research similar games** (3-5 examples with dev data)
4. **Assess feasibility** (technical, scope, timeline)
5. **Identify risks** (high/medium/low with mitigations)
6. **Generate recommendations** (proceed/adjust/reconsider with specifics)
7. **Create validation report** (following output format template)
8. **Offer follow-up** (prototype planning, GDD, alternatives)

**This skill empowers users to make informed decisions about their game concepts, setting them up for realistic success.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cautiouskurns) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

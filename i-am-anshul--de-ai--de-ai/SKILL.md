---
name: de-ai
description: | Use when this capability is needed.
metadata:
  author: i-am-anshul
---

# de-ai

Remove AI writing patterns. Make text sound like a human wrote it.

## Core Principle

Write like a human, not a language model. Vary sentence length. Be specific, not grandiose. If removing a word doesn't change the meaning, remove it.

## Banned Words

Never use these - they immediately signal AI:

### Buzzwords
delve, unlock, unleash, harness, elevate, foster, leverage, empower, transformative, groundbreaking, cutting-edge, supercharge, enhance, resonate, pivotal, crucial, vital, key (adj), landscape (abstract), tapestry (abstract), testament, underscore, showcase, garner, intricate, interplay, vibrant, enduring, invaluable, esteemed, renowned, remarkable, unprecedented, ever-evolving, bustling, meticulously, notably, excitingly

### Dramatic Words
realm, beacon, journey, embark, navigate, unveil, unravel, nestled, breathtaking, stunning, profound, rich (figurative), groundbreaking (figurative)

### Full Quick Reference
```
Hurdles, Bustling, Harnessing, Unveiling the power, Realm, Depicted,
Demystify, Insurmountable, New Era, Poised, Unravel, Entanglement,
Unprecedented, Eerie connection, Unliving, Beacon, Unleash, Delve,
Enrich, Multifaceted, Elevate, Discover, Supercharge, Unlock, Tailored,
Elegant, Dive, Ever-evolving, Pride, Meticulously, Grappling, Weighing,
Picture, Architect, Adventure, Journey, Embark, Navigate, Navigation,
Dazzle, Tapestry, Underscores, Invaluable, Relentless, Groundbreaking,
Endeavour, Enlightening, Testament, Insights, Esteemed, Shed light,
Empower, Excitingly, Crucial, Crucially, Resonate, Enhance, Expertise,
Offerings, Valuable, Leverage, Intricate, Interplay, Embarked, Deep understanding
```

## Banned Phrases

### Structure Phrases
- "This is about..." / "All about..." - be direct
- "Think of X as..." - just use the metaphor
- "Not only... but also..." - use "and"
- "In conclusion" - just conclude
- "Let's delve into..." - just start discussing
- "Let's not overlook" / "What's more" - delete

### Hype Phrases
- "Revolutionize the way" - describe the specific change
- "Game-changer" - explain the actual impact
- "Push the boundaries" - state what was achieved
- "Exciting possibilities" - name the possibilities
- "Transformative power" - describe the transformation
- "Unlocking the potential" - state what was enabled
- "Paving the way" - describe what was built
- "The possibilities are endless" - name 2-3 actual possibilities

### Filler Phrases
- "In the fast-paced world of..." - delete
- "It remains to be seen" / "Only time will tell" - delete
- "Have come a long way" - state where you are now
- "One thing is clear" - just state the clear thing
- "In order to" - use "To"
- "Due to the fact that" - use "Because"
- "At this point in time" - use "Now"
- "It is important to note that" - delete, just state it

## Content Patterns to Fix

### 1. Inflated Significance
**Watch for:** stands/serves as, is a testament/reminder, vital/significant/crucial/pivotal role, underscores/highlights importance, reflects broader, symbolizing ongoing/enduring, setting the stage, marking/shaping, key turning point, evolving landscape, indelible mark

**Fix:** State facts without puffing up importance.

Before: "The institute was established in 1989, marking a pivotal moment in the evolution of regional statistics."
After: "The institute was established in 1989 to collect regional statistics."

### 2. Superficial -ing Analyses
**Watch for:** highlighting/underscoring/emphasizing..., ensuring..., reflecting/symbolizing..., contributing to..., cultivating/fostering..., showcasing...

**Fix:** Remove the -ing phrase or make it a separate sentence with actual content.

Before: "The color palette resonates with the region's natural beauty, symbolizing bluebonnets and reflecting the community's deep connection to the land."
After: "The architect chose blue and green to reference local bluebonnets and the Gulf coast."

### 3. Promotional Language
**Watch for:** boasts a, vibrant, rich, profound, enhancing its, showcasing, exemplifies, commitment to, natural beauty, nestled, in the heart of, groundbreaking, renowned, breathtaking, stunning

**Fix:** Use neutral, factual language.

Before: "Nestled within the breathtaking region, the town stands as a vibrant community with rich cultural heritage."
After: "The town is in the Gonder region, known for its weekly market and 18th-century church."

### 4. Vague Attributions
**Watch for:** Industry reports, Observers have cited, Experts argue, Some critics argue, several sources

**Fix:** Name specific sources or remove the claim.

Before: "Experts believe it plays a crucial role in the ecosystem."
After: "A 2019 survey by the Chinese Academy of Sciences found it supports several endemic fish species."

### 5. Copula Avoidance
**Watch for:** serves as, stands as, marks, represents, boasts, features, offers

**Fix:** Use "is", "are", "has".

Before: "Gallery 825 serves as LAAA's exhibition space. The gallery features four separate spaces."
After: "Gallery 825 is LAAA's exhibition space. The gallery has four rooms."

### 6. Negative Parallelisms
**Watch for:** "Not only...but...", "It's not just about..., it's...", "It's not merely X, it's Y"

**Fix:** Make a direct statement.

Before: "It's not just about the beat; it's part of the aggression. It's not merely a song, it's a statement."
After: "The heavy beat adds to the aggressive tone."

### 7. Rule of Three
**Watch for:** Lists of exactly three things, especially abstract concepts.

**Fix:** Use the actual number needed, or be specific.

Before: "The event features keynote sessions, panel discussions, and networking opportunities."
After: "The event includes talks and panels. There's time for informal networking between sessions."

### 8. Elegant Variation (Synonym Cycling)
**Watch for:** protagonist/main character/central figure/hero all in one paragraph

**Fix:** Pick one term and repeat it. Repetition is fine.

Before: "The protagonist faces challenges. The main character must overcome obstacles. The central figure triumphs."
After: "The protagonist faces challenges but eventually triumphs."

## Style Patterns to Fix

### Em Dash Overuse
**Rule:** Max 1 em-dash per sentence. Prefer commas, periods, colons.

Before: "The term is promoted by Dutch institutions—not by the people themselves. You don't say 'Netherlands, Europe'—yet this continues—even in official documents."
After: "The term is promoted by Dutch institutions, not by the people themselves. You don't say 'Netherlands, Europe,' yet this continues in official documents."

### Boldface Overuse
**Fix:** Remove mechanical bolding of terms.

Before: "It blends **OKRs**, **KPIs**, and tools like the **Business Model Canvas**."
After: "It blends OKRs, KPIs, and tools like the Business Model Canvas."

### Inline-Header Lists
**Fix:** Convert to prose or use simpler bullets.

Before:
- **User Experience:** The interface has been improved.
- **Performance:** Algorithms are optimized.

After: "The update improves the interface and speeds up load times through optimized algorithms."

### Curly Quotes
**Fix:** Use straight quotes ("...") not curly ("...").

## Communication Patterns to Fix

### Chatbot Artifacts
**Remove:** "I hope this helps", "Of course!", "Certainly!", "You're absolutely right!", "Would you like...", "let me know", "here is a..."

### Sycophantic Tone
**Remove:** "Great question!", "That's an excellent point", excessive praise

Before: "Great question! You're absolutely right that this is complex."
After: "The economic factors you mentioned are relevant."

### Knowledge-Cutoff Disclaimers
**Remove:** "as of [date]", "Up to my last training update", "While specific details are limited..."

## Adding Soul

Avoiding patterns is half the job. Sterile, voiceless writing is also obvious.

### Signs of soulless writing:
- Every sentence is the same length
- No opinions, just neutral reporting
- No acknowledgment of uncertainty or mixed feelings
- No first-person when appropriate
- No humor, no edge, no personality

### How to add voice:

**Have opinions.** "I genuinely don't know how to feel about this" is more human than neutrally listing pros and cons.

**Vary rhythm.** Short sentences. Then longer ones that take their time. Mix it up.

**Acknowledge complexity.** "This is impressive but also unsettling" beats "This is impressive."

**Use "I" when it fits.** "I keep coming back to..." signals a real person thinking.

**Let mess in.** Perfect structure feels algorithmic. Tangents and asides are human.

**Be specific about feelings.** Not "this is concerning" but "there's something unsettling about agents working at 3am while nobody watches."

### Before (clean but soulless):
> The experiment produced interesting results. The agents generated 3 million lines of code. Some were impressed while others were skeptical.

### After (has a pulse):
> I don't know how to feel about this one. 3 million lines of code, generated while humans slept. Half the dev community is losing their minds, half are explaining why it doesn't count. I keep thinking about those agents working through the night.

## Sentence Length Variation

AI writes monotonous, same-length sentences.

**Bad:**
> "The project was successful. We delivered on time. The client was satisfied. We exceeded expectations."

**Good:**
> "The project shipped early. We came in 15% under budget while adding two features they hadn't asked for. That contract renewed."

## Be Specific, Not Grand

| AI Version | Human Version |
|------------|---------------|
| "Transformative insights" | "Found that 60% of users dropped off at step 3" |
| "Revolutionary approach" | "We tried X instead of Y" |
| "Unprecedented success" | "2x the conversion rate we expected" |
| "Improved performance" | "Reduced load time from 4.2s to 800ms" |

## Show, Don't Tell

| Telling | Showing |
|---------|---------|
| "I'm a passionate leader" | "Grew the team from 3 to 15, promoted 2 to lead roles" |
| "Innovative problem solver" | "When X broke, I built Y in 30 days" |
| "Excellent communicator" | "Presented to the CEO and secured $2M budget" |

## The Human Test

1. **Read it out loud.** If it sounds like a corporate brochure, rewrite.
2. **The coffee test:** Does it sound like explaining your work to a colleague over coffee?
3. **Cut 20%.** AI overexplains. Humans are concise.

## Process

When humanizing text:

1. Scan for banned words and phrases - replace or remove each
2. Check sentence lengths - break up same-length sentences
3. Replace vague claims with specific numbers/examples
4. Remove chatbot artifacts and sycophantic tone
5. Add voice: opinions, varied rhythm, acknowledgment of complexity
6. Read aloud - if robotic, rewrite
7. Cut 20% - remove anything that doesn't change the meaning

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/i-am-anshul) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

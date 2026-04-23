---
name: video-script-writer
description: Creates video scripts, narration text, and voiceover content for e-learning. Use when writing video scripts, narration, voiceover text, talking points, or when user mentions "video script," "narration," "voiceover," "audio script," or "presenter notes.
metadata:
  author: webmasterarbez
---

# Video Script Writing

Guide for creating effective video scripts and narration for e-learning content.

## Script Formats

### Two-Column Script (Standard)

Use for videos with visuals + narration:

```
VIDEO/VISUAL                    | AUDIO/NARRATION
--------------------------------|--------------------------------
[TITLE SLIDE]                   |
Company logo animates in        | (NO NARRATION)
Title: "Customer Service        |
Excellence"                     |
                                |
[PRESENTER - Medium shot]       | Welcome to Customer Service
Presenter on camera             | Excellence. I'm Sarah Chen,
                                | and I'll be your guide today.
                                |
[SCREEN RECORDING]              | Let's start by opening the
CRM dashboard loads             | CRM dashboard. You'll see
Cursor highlights main menu     | the main menu on the left
                                | side of your screen.
                                |
[GRAPHIC]                       | There are three key areas
Animated diagram shows          | we'll focus on: customer
3 areas highlighting            | lookup, ticket creation,
                                | and resolution tracking.
```

### Single-Column Script (Narration Only)

Use for voiceover with separate visual storyboard:

```markdown
## Screen 1: Introduction
**Duration:** 30 seconds

Welcome to the Safety Fundamentals course. In the next 20 minutes, you'll learn how to identify workplace hazards and respond to emergencies.

By the end of this module, you'll be able to:
- Recognize the five most common workplace hazards
- Apply the STOP protocol when you see a safety issue
- Report incidents using the correct procedure

Let's get started.

---

## Screen 2: What is a Hazard?
**Duration:** 45 seconds

A hazard is anything that could cause harm to you or your coworkers. Hazards can be physical, chemical, biological, or ergonomic.

[PAUSE for visual transition]

Physical hazards include things like wet floors, exposed wires, or blocked exits. These are often the easiest to spot—and fix.

Look around your workspace right now. Can you identify any physical hazards?
```

## Writing Guidelines

### Timing

| Content Type | Words per Minute | Example |
|--------------|------------------|---------|
| Slow/technical | 120-130 wpm | Complex procedures |
| Moderate | 140-150 wpm | Standard narration |
| Conversational | 150-160 wpm | Casual explanations |

**Rule of thumb:** 150 words = ~1 minute of narration

### Sentence Structure

- **Short sentences**: 10-15 words ideal for narration
- **One idea per sentence**: Easier to follow audibly
- **Natural pauses**: Use periods, not commas, for breathing room

### Before (Written style)
> The implementation of this procedure, which was developed by our compliance team in collaboration with external consultants, requires careful attention to detail.

### After (Spoken style)
> Our compliance team developed this procedure. It requires careful attention to detail. Let me walk you through it step by step.

### Verbal Signposts

Guide listeners through content:

| Purpose | Phrases |
|---------|---------|
| Introducing | "Let's start with..." / "First, we'll look at..." |
| Transitioning | "Now that you understand X, let's move to Y..." |
| Emphasizing | "This is important..." / "Pay close attention to..." |
| Summarizing | "To recap..." / "Remember these key points..." |
| Concluding | "You've now learned..." / "Next, you'll practice..." |

## Script Templates

### Explainer Video (2-3 minutes)

```markdown
## [Topic] Explainer

**Total Duration:** 2:30
**Tone:** Conversational, enthusiastic

---

### HOOK (0:00-0:15)
[Attention-grabbing opening]

Have you ever [relatable problem]? You're not alone. Today, we'll solve that problem in just a few minutes.

---

### INTRO (0:15-0:30)
[Set expectations]

I'm going to show you exactly how to [outcome]. By the end of this video, you'll be able to [specific skill].

---

### CONTENT SECTION 1 (0:30-1:15)
[First main point]

Let's start with [concept 1].

[Explanation - 2-3 sentences]

For example, [concrete example].

---

### CONTENT SECTION 2 (1:15-2:00)
[Second main point]

Now let's look at [concept 2].

[Explanation - 2-3 sentences]

Here's how this works in practice: [example]

---

### SUMMARY (2:00-2:20)
[Reinforce key points]

Let's recap what you've learned:
- First, [key point 1]
- Second, [key point 2]
- And finally, [key point 3]

---

### CALL TO ACTION (2:20-2:30)
[Next step]

Ready to try it yourself? Continue to the practice activity to apply what you've learned.
```

### Software Demo (Screen Recording)

```markdown
## How to [Task] in [Software]

**Duration:** 4:00
**Tone:** Clear, patient, instructional

---

### INTRO (0:00-0:20)

In this demo, I'll show you how to [task] in [software]. This is something you'll do regularly, so pay attention to the shortcuts I'll share along the way.

---

### STEP 1 (0:20-1:00)

First, open [software] and navigate to [location].

[PAUSE while showing navigation]

Click on [button/menu]. You'll see [what appears].

**Pro tip:** You can also use the keyboard shortcut [shortcut] to get here faster.

---

### STEP 2 (1:00-2:00)

Now, [action].

Notice how [observation about the interface].

If you see [potential issue], don't worry—just [solution].

[PAUSE for visual]

---

### STEP 3 (2:00-3:00)

Next, we'll [action].

[Detailed walkthrough with pauses]

And that's it. You've successfully [completed task].

---

### COMMON ISSUES (3:00-3:40)

Before we wrap up, let me show you how to handle a common issue.

If [problem], try [solution].

If that doesn't work, [alternative].

---

### WRAP-UP (3:40-4:00)

You now know how to [task]. Remember:
- [Key takeaway 1]
- [Key takeaway 2]

Practice this a few times, and it'll become second nature.
```

## Narration Best Practices

### Do's

- Write for the ear, not the eye
- Read scripts aloud while writing
- Include pronunciation guides for technical terms: "API (A-P-I)"
- Add [PAUSE] markers for visual transitions
- Use contractions: "you'll" not "you will"

### Don'ts

- Don't narrate what's obvious on screen: ~~"As you can see..."~~
- Don't use jargon without explanation
- Don't cram too much into one breath
- Don't forget accessibility (captions needed)

## Audio Direction Notes

Include these cues for voice talent:

```
[EMPHASIS on "never"]
[SLOWER PACE for next paragraph]
[UPBEAT TONE]
[PAUSE 2 seconds]
[SPELL OUT: S-M-E]
```

## Quality Checklist

- [ ] Script reads naturally when spoken aloud
- [ ] Timing calculated (aim for 150 wpm)
- [ ] Sentences are short (10-15 words average)
- [ ] Technical terms have pronunciation guides
- [ ] Pauses marked for visual transitions
- [ ] Contractions used for natural speech
- [ ] Verbal signposts guide the listener
- [ ] Matches visual storyboard/shot list
- [ ] Accessibility: Can be captioned accurately

## File Output

Save scripts to: `course-template/02-development/modules/module-XX/assets/audio/`

Naming: `script-[module]-[screen].md` or `narration-[module]-[topic].md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/webmasterarbez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

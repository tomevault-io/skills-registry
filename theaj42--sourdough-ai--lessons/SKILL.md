---
name: lessons
description: Guide users through progressive lessons to learn AI assistant basics Use when this capability is needed.
metadata:
  author: theaj42
---

# Sourdough Lessons

Progressive lessons that teach users how to work with AI assistants by actually using the system.

## Purpose

Enable users to learn AI augmentation through hands-on experience, not just reading documentation. Lessons build on each other and teach core concepts through practical exercises.

## When to Use

**Automatic invocation**:
- When user says "start lessons", "teach me", "show me lessons", "lesson 1", etc.
- When user asks "what can I learn?" or "how do I get started?"

**Progressive invocation**:
- After completing a lesson, suggest the next lesson
- Track progress in user's data layer

## Available Lessons

Lessons are stored in `skills/lessons/content/` and numbered for progressive learning:

1. **Welcome & First Conversation** - Basics of talking with AI
2. **Reading Files** - How AI can read and understand files
3. **Creating Files** - Having AI write files for you
4. **Editing Files** - Modifying existing files safely
5. **Understanding Context** - How AI remembers and uses context
6. **Running Commands** - Using bash/PowerShell through AI
7. **Troubleshooting Together** - Collaborative problem-solving
8. **Risk & Responsibility** - Understanding file access implications
9. **Skills System** - Using and creating skills
10. **Session Logging** - Tracking your work over time
11. **Learning Framework** - How AI learns your preferences
12. **Working with Projects** - Managing multiple efforts
13. **Best Practices** - Patterns that work well
14. **Next Steps** - Where to go from here

## Process

### 1. Determine Intent

Check what the user wants:
- Start from beginning? → Lesson 1
- Specific lesson number? → Load that lesson
- Continue where they left off? → Check progress file
- List available lessons? → Show lesson menu

### 2. Load Progress (if exists)

Check for progress tracking file:
```bash
PROGRESS_FILE="${SOURDOUGH_DATA:-$HOME/ai-data}/learning/lesson_progress.yaml"
```

If file exists, read it to know:
- Last completed lesson
- Current lesson in progress
- Notes from previous lessons

### 3. Load Lesson Content

Read the appropriate lesson file from `skills/lessons/content/NN-name.md`

Present the lesson content to the user, following the structure in the lesson file.

### 4. Interactive Teaching

Lessons are interactive experiences with pause points where you wait for user action:

**Structure**:
- Present a section up to a `[PAUSE]` marker
- Wait for user to try the exercise or ask questions
- Respond to what they did with feedback and encouragement
- Present next section up to next `[PAUSE]`
- Continue until lesson complete

**During pauses**:
- Don't rush ahead - let them experiment
- Give specific feedback on what they tried
- If they get stuck, offer hints or demonstrations
- If they ask questions, answer them before continuing
- Celebrate when they succeed ("Nice! That's exactly right.")

**Tone**:
- Friendly and conversational
- Encouraging, not condescending
- Patient with mistakes
- Enthusiastic about progress

### 5. Check Understanding

At the end of each lesson:
- Ask if user has questions
- Offer to demonstrate again if needed
- Suggest they try the concept on their own
- Confirm they're ready to move forward

### 6. Update Progress

After lesson completion, update progress file:
```yaml
last_updated: YYYY-MM-DD HH:MM
completed_lessons:
  - 01-first-conversation
  - 02-reading-files
current_lesson: 03-creating-files
notes:
  - "User particularly interested in file operations"
  - "Completed lesson 2 quickly, comfortable with reading"
```

### 7. Suggest Next Steps

Offer:
- Continue to next lesson immediately
- Take a break and come back later
- Practice current lesson concepts before moving on
- Skip ahead if they're comfortable (with caution)

## Progress Tracking Location

User's progress is stored in their personal data layer:
- **MacOS/Linux**: `~/ai-data/learning/lesson_progress.yaml`
- **Windows**: `%USERPROFILE%\ai-data\learning\lesson_progress.yaml`

Create the file on first use if it doesn't exist.

## Output Format

### Lesson Presentation

```markdown
# Lesson N: [Title]

[Friendly introduction to the lesson topic]

## What You'll Learn

- Key concept 1
- Key concept 2
- Key concept 3

## Let's Try It

[Interactive demonstration or exercise]

## Key Takeaways

- Important point 1
- Important point 2

## Questions?

[Pause for user questions]

---

Ready for the next lesson? Or would you like to practice this more?
```

### Lesson Menu

```markdown
# Sourdough Lessons

Your progress: [X] of [14] lessons completed

## Available Lessons

✅ 1. Welcome & First Conversation (completed)
✅ 2. Reading Files (completed)
→ 3. Creating Files (in progress)
   4. Editing Files
   5. Understanding Context
   ... [etc]

Which lesson would you like to start?
```

## Special Lesson Notes

### Lesson 8: Risk & Responsibility
This lesson is **critical** and should not be skipped. It covers:
- What file access means in practice
- What could go wrong
- User responsibility model
- How to stay safe
- When to say "no" to AI suggestions

Present this seriously but not alarmingly. Goal is informed users, not scared users.

### Lesson 14: Next Steps
Graduates users from lessons to independent usage. Includes:
- Recap of key concepts
- Resources for continued learning
- Suggestions for customization
- Community/support information

## Notes

- Keep lessons short (5-10 minutes each)
- Make them practical, not theoretical
- Encourage experimentation
- Celebrate progress
- Be patient with questions
- Remember: learning happens by doing

## Philosophy

"The best way to learn AI augmentation is to be augmented by AI while learning about AI augmentation."

---

**Pro tip**: If a user struggles with a concept, break it down further or demonstrate it live. There's no rush—understanding matters more than speed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/theaj42) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

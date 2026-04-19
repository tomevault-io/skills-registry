---
name: learning-quiz-generator
description: Generate comprehensive multiple-choice quizzes on any topic with tricky edge cases and gotchas to ensure deep understanding. Tests nuances, not just surface knowledge. Use when this capability is needed.
metadata:
  author: capicuaman
---

# Learning Quiz Generator Skill

This skill creates challenging multiple-choice quizzes designed to test **deep understanding** and catch **common misconceptions**. Questions focus on edge cases, gotchas, and nuances that separate surface knowledge from mastery.

## When to Use This Skill

Use this skill when the user wants to:
- Test their understanding of a concept
- Create study materials with tricky questions
- Identify knowledge gaps and misconceptions
- Prepare for deep technical discussions
- Practice with edge-case scenarios
- Learn through deliberate practice

## Quiz Design Philosophy

### Core Principles:
1. **Test Understanding, Not Memory** - Focus on "why" and "how", not "what"
2. **Include Gotchas** - Common misconceptions that seem right but aren't
3. **Edge Cases Matter** - Test boundary conditions and special scenarios
4. **Realistic Distractors** - Wrong answers should be plausible and educational
5. **Progressive Difficulty** - Start approachable, end challenging
6. **Explanatory Feedback** - Every answer needs a detailed explanation

### Question Quality Standards:
- ✅ Tests a specific nuance or edge case
- ✅ Has one clearly correct answer (no ambiguity)
- ✅ Distractors represent common misconceptions
- ✅ Requires understanding, not just recall
- ✅ Includes context and realistic scenarios
- ❌ Avoid trivial "definition lookup" questions
- ❌ Avoid trick questions that rely on wordplay
- ❌ Avoid questions with subjective answers

## Quiz Generation Workflow

### Step 1: Topic Analysis
When user requests a quiz on [TOPIC]:

1. **Identify Core Concepts**
   - List fundamental concepts in the topic
   - Note relationships and dependencies
   - Identify common learning paths

2. **Map Gotchas & Edge Cases**
   - What do beginners typically misunderstand?
   - What are the boundary conditions?
   - What seems intuitive but is actually wrong?
   - What are the common pitfalls?

3. **Assess Complexity Levels**
   - Beginner: Basic concepts with common mistakes
   - Intermediate: Interactions between concepts
   - Advanced: Edge cases and optimization trade-offs

### Step 2: Question Design

For each question, create:

```markdown
## Question [N]: [Brief Topic]

[Scenario or context setup - make it realistic]

[The actual question]

A) [Plausible wrong answer - common misconception #1]
B) [Correct answer]
C) [Plausible wrong answer - common misconception #2]
D) [Plausible wrong answer - edge case confusion]

**Correct Answer: [Letter]**

**Explanation:**
[Why the correct answer is right - deep reasoning]

**Why Other Options Are Wrong:**
- **A:** [Explain the misconception and why it fails]
- **C:** [Explain the edge case and why it fails]
- **D:** [Explain the confusion and why it fails]

**Key Takeaway:** [One-liner lesson from this question]
```

### Step 3: Question Categories to Include

Every comprehensive quiz should cover:

1. **Fundamentals with Gotchas (20-30%)**
   - Basic concepts that people think they understand
   - Common misapplications of simple rules

2. **Interaction & Dependencies (30-40%)**
   - How concepts work together
   - Priority and precedence rules
   - When rules conflict or override

3. **Edge Cases & Boundaries (20-30%)**
   - Limit conditions
   - Special scenarios
   - Exception handling

4. **Best Practices & Trade-offs (10-20%)**
   - When to use what
   - Performance implications
   - Maintainability considerations

### Step 4: Difficulty Distribution

**Question Count Guidelines:**
- Quick Check: 5-7 questions
- Standard Quiz: 10-15 questions
- Comprehensive Test: 20-30 questions
- Mastery Assessment: 30+ questions

**Difficulty Curve:**
- Questions 1-3: Warm-up (70% should get these right)
- Questions 4-7: Core understanding (50% should get these right)
- Questions 8+: Advanced nuances (30% should get these right)

### Step 5: Format & Delivery

**Default Format:**
```markdown
# [Topic] - Deep Understanding Quiz

**Total Questions:** [N]
**Estimated Time:** [X] minutes
**Passing Score:** [Y]% (indicates solid understanding)

---

[Questions with explanations at the end OR after each question]

---

## Answer Key

[Summary of correct answers]

## Scoring Guide
- 90-100%: Mastery - Deep understanding
- 75-89%: Strong - Minor gaps to address
- 60-74%: Moderate - Review key concepts
- Below 60%: Review needed - Start with fundamentals
```

**Interactive Format Option:**
- Show question
- Wait for user answer
- Reveal explanation
- Track score in real-time
- Provide personalized recommendations at the end

## Advanced Features

### Adaptive Questioning
If user requests adaptive quiz:
1. Start with medium difficulty
2. If they get it right → harder question
3. If they get it wrong → related easier question
4. Focus on weak areas

### Spaced Repetition
If user wants to review over time:
1. Generate quiz with unique ID
2. Track which questions they missed
3. Re-test on missed concepts after delay
4. Increase intervals as they improve

### Topic Deep-Dives
If user requests focus on specific subtopic:
1. Generate 5-10 questions on just that area
2. Cover edge cases extensively
3. Include comparison questions with related topics

## Example Question Types

### Type 1: Scenario-Based
```
You're working in project A and create a skill in
~/.claude/projects/-project-A/skills/my-skill/.
Then you `cd` to project B and try to use the skill.
What happens?

[Options testing understanding of project scoping]
```

### Type 2: Edge Case Identification
```
Which of these directory paths would NOT work as
a valid project key in Claude Code?

[Options testing understanding of path conversion rules]
```

### Type 3: Priority & Precedence
```
You have the same skill defined in three locations:
- ~/.claude/skills/
- ~/.claude/projects/current/skills/
- ~/.claude/plugins/.../skills/

Which one takes precedence?

[Options testing skill loading order]
```

### Type 4: Troubleshooting
```
Your skill isn't loading. Which is LEAST likely to be the issue?

[Options testing common failure modes]
```

### Type 5: Best Practice
```
You want to share a Remotion workflow skill across 3 related
video projects. What's the BEST approach?

[Options testing understanding of symlinks, copying, global skills]
```

## Gotcha Patterns to Include

Common patterns that trap learners:

1. **Seems Right But Isn't**
   - Intuitive but incorrect rules
   - Overgeneralized patterns

2. **Works But Shouldn't Be Used**
   - Technically possible but bad practice
   - Non-idiomatic solutions

3. **Special Cases**
   - Exceptions to general rules
   - Context-dependent behavior

4. **Common Confusions**
   - Similar concepts with subtle differences
   - Terminology mix-ups

## Quality Checklist

Before delivering quiz, verify:
- [ ] Each question tests understanding, not just memory
- [ ] Distractors represent real misconceptions
- [ ] Every answer has detailed explanation
- [ ] Questions progress from approachable to challenging
- [ ] Mix of scenario-based, edge case, and best practice questions
- [ ] No ambiguous questions
- [ ] No questions with multiple correct answers
- [ ] Explanations teach additional concepts
- [ ] Key takeaways are actionable

## User Customization Options

Users can request:
- **Length:** "Quick 5-question check" vs "Comprehensive 30-question test"
- **Difficulty:** "Beginner level" vs "Expert level" vs "Mixed"
- **Format:** "All questions upfront" vs "Interactive one-at-a-time"
- **Focus:** "Focus on edge cases" vs "Focus on best practices"
- **Style:** "Scenario-based" vs "Direct questions"

## Post-Quiz Actions

After quiz completion:
1. **Score Analysis**
   - Calculate percentage correct
   - Identify weak areas
   - Compare to typical results

2. **Personalized Recommendations**
   - "Review [concept] - you got 2/3 questions wrong"
   - "You're strong on basics, focus on edge cases"
   - "Consider practicing [specific scenario]"

3. **Follow-Up Resources**
   - Suggest documentation to review
   - Offer to create practice exercises
   - Generate deep-dive on weak topics

4. **Save Progress**
   - Offer to save quiz results
   - Track improvement over time
   - Suggest when to re-test

---

## Meta: Using This Skill

When user says:
- "Quiz me on [topic]"
- "Test my understanding of [concept]"
- "Create study questions for [subject]"
- "Make a tricky quiz about [area]"

Then:
1. Activate this skill
2. Analyze the topic for gotchas
3. Generate questions following these guidelines
4. Deliver with explanations
5. Provide scoring and recommendations

This skill is **globally available** because learning acceleration applies to any project or topic!

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/capicuaman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

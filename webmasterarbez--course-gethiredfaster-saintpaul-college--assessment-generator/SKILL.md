---
name: assessment-generator
description: Generates quiz questions, scenarios, and assessments aligned to learning objectives. Use when creating quizzes, test questions, knowledge checks, scenarios, question banks, or when user mentions "quiz," "assessment," "test questions," "knowledge check," or "question bank.
metadata:
  author: webmasterarbez
---

# Assessment Generator

Guide for creating effective assessments aligned to learning objectives.

## Assessment Alignment

### Match Question Type to Objective Level

| Bloom's Level | Question Types | Example Stems |
|---------------|----------------|---------------|
| **Remember** | Multiple choice, True/False, Matching | "Which of the following...", "Select the correct..." |
| **Understand** | Multiple choice (interpretation), Short answer | "What does X mean?", "Explain why..." |
| **Apply** | Scenario-based, Calculation, Demonstration | "Given this situation...", "Calculate the..." |
| **Analyze** | Case study, Compare/contrast | "What is the root cause?", "How do A and B differ?" |
| **Evaluate** | Critique, Recommendation, Justification | "Which approach is best?", "Evaluate the..." |
| **Create** | Project, Design, Open-ended | "Design a...", "Develop a plan for..." |

## Question Templates

### Multiple Choice

```markdown
**Objective:** [The objective this question assesses]
**Bloom's Level:** [Remember/Understand/Apply/Analyze/Evaluate]

**Question:**
[Clear, complete question ending with ?]

**Options:**
a) [Distractor - common misconception]
b) [Distractor - partially correct]
c) [Correct answer]
d) [Distractor - plausible but wrong]

**Correct Answer:** c

**Feedback - Correct:**
[Reinforce why this is right]

**Feedback - Incorrect:**
[Explain the correct answer without just restating it]
```

### True/False

```markdown
**Objective:** [Objective]
**Bloom's Level:** Remember/Understand

**Statement:**
[Clear, unambiguous statement that is definitively true or false]

**Correct Answer:** True / False

**Feedback - Correct:**
[Confirmation]

**Feedback - Incorrect:**
[Clarification with correct information]
```

### Matching

```markdown
**Objective:** [Objective]
**Bloom's Level:** Remember/Understand

**Instructions:** Match each term to its definition.

**Terms:**
1. [Term A]
2. [Term B]
3. [Term C]
4. [Term D]

**Definitions:**
a) [Definition for Term B]
b) [Definition for Term D]
c) [Definition for Term A]
d) [Definition for Term C]

**Correct Matches:**
1-c, 2-a, 3-d, 4-b
```

### Scenario-Based

```markdown
**Objective:** [Objective]
**Bloom's Level:** Apply/Analyze/Evaluate

**Scenario:**
[CONTEXT: Who, what, where - 2-3 sentences]
[SITUATION: The specific challenge - 2-3 sentences]
[DATA: Relevant facts for decision-making]
- [Fact 1]
- [Fact 2]
- [Fact 3]

**Question:**
[What decision/action is required?]

**Options:**
a) [Option - reasonable but not optimal]
b) [Option - correct answer]
c) [Option - common mistake]
d) [Option - plausible but wrong approach]

**Correct Answer:** b

**Feedback - Correct:**
[Explain why this is the best approach given the scenario]

**Feedback - Incorrect:**
[Explain the reasoning for the correct answer; address why other options fall short]
```

### Fill-in-the-Blank

```markdown
**Objective:** [Objective]
**Bloom's Level:** Remember

**Question:**
The [BLANK] model consists of five phases: Analyze, Design, Develop, Implement, and Evaluate.

**Correct Answer(s):** ADDIE, addie

**Feedback - Correct:**
[Confirmation]

**Feedback - Incorrect:**
[Provide correct answer with brief explanation]
```

### Short Answer

```markdown
**Objective:** [Objective]
**Bloom's Level:** Understand/Apply

**Question:**
In 2-3 sentences, explain [concept/process].

**Sample Answer:**
[Model answer demonstrating expected response]

**Scoring Rubric:**
- 2 points: [Criteria for full credit]
- 1 point: [Criteria for partial credit]
- 0 points: [Criteria for no credit]
```

## Question Bank Generator

### Generate Varied Questions for One Objective

Given an objective, create questions at multiple difficulty levels:

```markdown
## Objective: [State the learning objective]

### Level 1 - Knowledge Check (Easy)
[Basic recall question - Multiple choice or True/False]

### Level 2 - Comprehension (Medium)
[Understanding question - Explain or interpret]

### Level 3 - Application (Medium-Hard)
[Apply to new situation - Scenario-based]

### Level 4 - Analysis/Evaluation (Hard)
[Analyze or evaluate - Case study or critique]
```

## Distractor Writing Guidelines

### Good Distractors

- Based on common misconceptions
- Plausible to someone who doesn't know the material
- Similar length and format to correct answer
- Grammatically consistent with the stem

### Avoid

- "All of the above" / "None of the above"
- Obviously wrong answers
- "Always" / "Never" (too absolute)
- Tricky wording designed to confuse
- Overlapping options

### Example - Poor Distractors

```
What is the first phase of ADDIE?
a) Implementation ← Out of order, too obviously wrong
b) Making stuff ← Not professional terminology
c) Analyze ← Correct
d) All phases happen at once ← Obviously wrong
```

### Example - Good Distractors

```
What is the first phase of ADDIE?
a) Design ← Common misconception (sounds like it should be first)
b) Assessment ← Sounds similar to Analyze
c) Analyze ← Correct
d) Discovery ← Similar "A" word, plausible synonym
```

## Assessment Output Formats

### JSON Format (for authoring tools)

```json
{
  "questions": [
    {
      "id": "q1",
      "type": "multiple-choice",
      "objective": "1.1",
      "bloomsLevel": "apply",
      "stem": "Question text here",
      "options": [
        {"id": "a", "text": "Option A", "correct": false},
        {"id": "b", "text": "Option B", "correct": true},
        {"id": "c", "text": "Option C", "correct": false},
        {"id": "d", "text": "Option D", "correct": false}
      ],
      "feedback": {
        "correct": "Correct! Because...",
        "incorrect": "The correct answer is B because..."
      },
      "points": 1
    }
  ]
}
```

### Markdown Table Format

```markdown
| # | Question | A | B | C | D | Answer | Objective |
|---|----------|---|---|---|---|--------|-----------|
| 1 | [Stem] | [Opt A] | [Opt B] | [Opt C] | [Opt D] | B | 1.1 |
| 2 | [Stem] | [Opt A] | [Opt B] | [Opt C] | [Opt D] | C | 1.2 |
```

## Quality Checklist

For each question:

- [ ] Maps directly to a learning objective
- [ ] Appropriate question type for Bloom's level
- [ ] Stem is clear and complete
- [ ] Only one unambiguous correct answer
- [ ] Distractors are plausible
- [ ] All options grammatically consistent
- [ ] No "tricks" or unnecessarily complex wording
- [ ] Feedback explains why answer is correct/incorrect
- [ ] Scenarios are realistic and relevant

For the assessment overall:

- [ ] Every objective has at least one question
- [ ] Mix of difficulty levels
- [ ] Appropriate length for time available
- [ ] Clear instructions provided
- [ ] Passing score is justified

## File Output

Save to: `course-template/02-development/modules/module-XX/assessments/`

Naming conventions:
- `quiz-knowledge-check-[topic].json`
- `quiz-final-module-XX.json`
- `question-bank-[topic].json`
- `scenario-[topic].md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/webmasterarbez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

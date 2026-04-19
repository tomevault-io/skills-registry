---
name: quizes-generator
description: Generate comprehensive quizzes with 80-100 multiple choice questions (MCQ) from a topic title or reference file. Questions include difficulty levels (Easy/Medium/Hard) with answer key at the end. Use when creating study materials, practice tests, or assessments. Use when this capability is needed.
metadata:
  author: umer-jamshed993
---

# Quizes Generator

Generate comprehensive multiple-choice quizzes with 80-100 questions for studying and assessment.

## Instructions

### When Given a Topic Title:

1. Research or use knowledge about the topic thoroughly
2. Generate **80-100 multiple choice questions** covering all aspects of the topic
3. Distribute difficulty levels evenly:
   - **Easy (30-35 questions):** Basic definitions, facts, and fundamental concepts
   - **Medium (30-35 questions):** Application, understanding, and connections
   - **Hard (20-30 questions):** Analysis, evaluation, and complex scenarios
4. Each question must have exactly 4 options (A, B, C, D)
5. Place the complete answer key at the end of the document

### When Given a Reference File:

1. Read the provided file using the Read tool
2. Extract all key concepts, facts, definitions, and important details
3. Create questions covering the entire content comprehensively
4. Generate 80-100 MCQs based on the material
5. Ensure questions test understanding, not just memorization

### Output Format:

Generate quizzes in this clean, organized markdown format:

```markdown
# Quiz: [Topic Name]

**Total Questions:** [80-100]
**Question Types:** Multiple Choice (MCQ)
**Difficulty Distribution:** Easy (30-35) | Medium (30-35) | Hard (20-30)

---

## Easy Questions

### Q1. [Question text here]
- A) Option A
- B) Option B
- C) Option C
- D) Option D

### Q2. [Question text here]
- A) Option A
- B) Option B
- C) Option C
- D) Option D

[Continue for all Easy questions...]

---

## Medium Questions

### Q36. [Question text here]
- A) Option A
- B) Option B
- C) Option C
- D) Option D

[Continue for all Medium questions...]

---

## Hard Questions

### Q71. [Question text here]
- A) Option A
- B) Option B
- C) Option C
- D) Option D

[Continue for all Hard questions...]

---

## Answer Key

| Q# | Answer | Q# | Answer | Q# | Answer | Q# | Answer |
|----|--------|----|--------|----|--------|----|--------|
| 1  | A      | 2  | C      | 3  | B      | 4  | D      |
| 5  | A      | 6  | B      | 7  | C      | 8  | A      |
[Continue for all answers in table format...]
```

### File Naming:

- Save as `quiz-[topic-name].md` in the current directory
- Use lowercase and hyphens for the topic name (e.g., `quiz-machine-learning.md`)

## Guidelines for Quality Questions

### Question Writing Rules:

1. **Clear and unambiguous** - Avoid confusing wording
2. **One correct answer** - Only one option should be definitively correct
3. **Plausible distractors** - Wrong options should be believable
4. **Avoid "All of the above"** - Use specific options instead
5. **No negative phrasing** - Avoid "Which is NOT..." when possible
6. **Test understanding** - Go beyond simple recall

### Difficulty Guidelines:

| Level | Characteristics | Example Focus |
|-------|-----------------|---------------|
| Easy | Definitions, basic facts, direct recall | "What is X?", "Define Y" |
| Medium | Application, comparison, relationships | "How does X relate to Y?", "Apply X to..." |
| Hard | Analysis, synthesis, evaluation, scenarios | "Given scenario X, what would happen if...?" |

### Content Coverage:

- Cover **all major topics** within the subject
- Include questions on:
  - Key terminology and definitions
  - Important concepts and principles
  - Real-world applications
  - Relationships between concepts
  - Common misconceptions (as wrong options)

## Examples

### Example 1: Topic-based Generation

**User Request:** Generate quiz on Python programming

**Output:** `quiz-python-programming.md`

```markdown
# Quiz: Python Programming

**Total Questions:** 90
**Question Types:** Multiple Choice (MCQ)
**Difficulty Distribution:** Easy (32) | Medium (33) | Hard (25)

---

## Easy Questions

### Q1. What is the correct file extension for Python files?
- A) .python
- B) .py
- C) .pt
- D) .pyt

### Q2. Which keyword is used to define a function in Python?
- A) function
- B) func
- C) def
- D) define

### Q3. What data type is the result of: type(3.14)?
- A) int
- B) str
- C) float
- D) double

[... continues to Q32]

---

## Medium Questions

### Q33. What is the output of: print(list(range(0, 10, 2)))?
- A) [0, 2, 4, 6, 8]
- B) [0, 2, 4, 6, 8, 10]
- C) [2, 4, 6, 8, 10]
- D) [1, 3, 5, 7, 9]

[... continues to Q65]

---

## Hard Questions

### Q66. In Python, what is the time complexity of checking membership in a set vs a list?
- A) Both are O(n)
- B) Set is O(1), List is O(n)
- C) Set is O(n), List is O(1)
- D) Both are O(1)

[... continues to Q90]

---

## Answer Key

| Q# | Answer | Q# | Answer | Q# | Answer | Q# | Answer | Q# | Answer |
|----|--------|----|--------|----|--------|----|--------|----|--------|
| 1  | B      | 2  | C      | 3  | C      | 4  | A      | 5  | B      |
| 6  | D      | 7  | A      | 8  | C      | 9  | B      | 10 | A      |
[... complete answer key]
```

### Example 2: File-based Generation

**User Request:** Generate quiz from chapter5-notes.pdf

**Process:**
1. Read the contents of chapter5-notes.pdf
2. Identify all key concepts and details
3. Create 80-100 MCQs covering the material
4. Distribute by difficulty level
5. Save as `quiz-chapter5-notes.md`

## Best Practices

- **Minimum 80 questions** - Never generate fewer than 80 questions
- **Maximum 100 questions** - Keep quizzes manageable
- **Balanced coverage** - Don't focus too heavily on one subtopic
- **Varied question stems** - Use different question formats (What, Which, How, Why)
- **Logical ordering** - Group related questions together within difficulty sections
- **Proofread options** - Ensure all options are grammatically consistent
- **Numbered sequentially** - Questions numbered 1 through total across all sections

## Requirements

- For reference files: File must be readable (txt, md, pdf, docx)
- Topic should be specific enough to generate meaningful questions
- Broad topics will focus on foundational and commonly tested concepts

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/umer-jamshed993) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

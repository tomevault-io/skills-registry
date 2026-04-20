---
name: create-quizelet-data
description: Transform technical interview problem collections into structured Quizlet datasets with multiple choice questions. Generates CSV files optimized for Quizlet import covering Big O complexity, technique/approach, and practical examples for each problem. Use this skill when preparing quiz materials from problem sets, mock interviews, or technical documentation. Use when this capability is needed.
metadata:
  author: pluto-atom-4
---

# Create Quizlet Data Skill

## Overview

This skill converts technical interview problem collections into comprehensive Quizlet datasets formatted for direct import. It generates multiple choice questions that test three dimensions of understanding: Big O complexity analysis, algorithmic techniques/approaches, and practical examples with real-world context.

## When to Use

Use this skill when you need to:
- Convert mock interview problem sets into Quizlet study material
- Create multiple choice questions for algorithm and data structure problems
- Generate Big O complexity questions from problem descriptions
- Create technique/approach questions to reinforce algorithmic thinking
- Generate example-based questions to test practical understanding
- Prepare for technical interviews with structured, reusable quiz content
- Build a repository of quiz datasets that can be imported into Quizlet

> **For usage instructions:** See [README.md](README.md) for detailed workflows on invoking this skill in Claude Code Agent and Copilot CLI.

## Dataset Structure

A Quizlet dataset should include:

### Question Types

1. **Big O Complexity Questions** - Tests time/space complexity understanding
   - Asks for specific Big O notation (O(n), O(n log n), etc.)
   - Provides multiple choice options
   - Validates complexity analysis knowledge

2. **Technique/Approach Questions** - Tests algorithm selection knowledge
   - Asks which technique or data structure to use
   - Tests understanding of why the approach is suitable
   - Reinforces algorithmic pattern recognition

3. **Example Questions** - Tests practical application
   - Asks about real-world examples or use cases
   - Tests understanding with concrete scenarios
   - Validates comprehension through examples

### CSV Format

The dataset uses Quizlet's standard CSV import format with double newlines for card separation:

```
Term,Definition
"Problem Name (Complexity)","(Multiple Choice)
Q1 - Complexity: What is the time complexity for [technique]?

A) [Option A]
B) [Option B]
C) [Option C]
D) [Option D]

✓ Correct: [Answer]


"Problem Name (Technique)","(Multiple Choice)
Q2 - Technique: Which [approach/structure] is used for [problem]?

A) [Option A]
B) [Option B]
C) [Option C]
D) [Option D]

✓ Correct: [Answer]


"Problem Name (Example)","(Multiple Choice)
Q3 - Example: [Real-world scenario question]?

A) [Option A]
B) [Option B]
C) [Option C]
D) [Option D]

✓ Correct: [Answer]"
```

**Key Features:**
- Single blank line after question (for spacing from options)
- Single blank line after options (before answer key)
- **Double newline (`\n\n`) between card entries** for clear card separation
- This improves readability both in the CSV and in Quizlet's display

## Naming Convention

Dataset files should follow this pattern:

- **Format:** `quiz[number]_[topic]_[style].csv`
- **Example:** `quiz1_algorithms_multiple_choice.csv`
- **Components:**
  - `quiz[number]`: Quiz identifier (e.g., quiz1, quiz2)
  - `[topic]`: General topic area (e.g., algorithms, data_structures, system_design)
  - `[style]`: Question format (e.g., multiple_choice, flashcard, true_false)

---

## Step-by-Step Implementation Guide

### 1. Problem Extraction
**What to include:**
- Clear problem title and context
- Time and space complexity from solution
- Technique or algorithm used
- Real-world example or use case
- From provided problem source (e.g., mock quiz, interview prep guide)

**Steps:**
1. Identify all problems in source material
2. For each problem, extract:
   - Problem name/title
   - Optimal time complexity
   - Optimal space complexity
   - Primary technique/algorithm
   - Real-world example or context

### 2. Question Generation

**For Big O Complexity Questions:**
- Create a question asking for time or space complexity
- Include 4 multiple choice options (correct + 3 distractors)
- Verify answer matches the provided solution
- Use common complexity notations: O(1), O(log n), O(n), O(n²), etc.
- Add blank line after question and before options for clarity

**For Technique Questions:**
- Ask which approach, algorithm, or data structure to use
- Include the problem scenario
- Provide 4 distinct options (1 correct, 3 common alternatives)
- Reinforce why the correct answer is best for this problem
- Separate options with blank line after question

**For Example Questions:**
- Ask about real-world applications or practical scenarios
- Base questions on examples provided in problem descriptions
- Include 4 options focusing on different use cases
- Test understanding through concrete scenarios
- Use blank lines to separate question and answer sections

### 3. CSV Formatting

**Structure:**
- Each question becomes a row in the CSV
- "Term" column: Contains problem name and question type
- "Definition" column: Contains formatted multiple choice content
- Include answer marker: `✓ Correct: [X) Answer text]`

**Best Practices:**
- Escape quotes properly in CSV (double quotes for CSV embedded quotes)
- Use consistent formatting across all questions
- Include visual indicators (Q1, Q2, Q3) for question sequence
- **Add single blank lines** after question and after options (within card)
- **Use double newlines (`\n\n`)** between card entries for separation
- Bold or clearly mark question text
- Place answer key at bottom of definition
- Double newline placement improves card distinction in CSV and Quizlet display

### 4. Quality Checklist

Before finalizing the dataset:

- [ ] All problems from source are represented
- [ ] Each problem has 3 question types (Big O, Technique, Example)
- [ ] All multiple choice options are distinct and plausible
- [ ] Correct answers are accurate per source material
- [ ] CSV is properly formatted and escapes quotes correctly
- [ ] File naming follows convention: `quiz[N]_[topic]_multiple_choice.csv`
- [ ] Dataset is saved in `generated/media/quizlet/` directory
- [ ] Questions test different dimensions of understanding
- [ ] Real-world examples are included and contextually relevant
- [ ] Complexity analysis matches provided solutions

---

## Example: Algorithms Quiz

For a problem: "Find duplicates in array [1-n] using Index Marking (O(n) time, O(1) space)"

**Generated Questions:**

1. **Big O Question**
   - Term: "Problem: Find duplicates in array [1-n]"
   - Definition: Q1 asking time complexity, 4 options, answer marked

2. **Technique Question**
   - Term: "Problem: Find duplicates in array [1-n]"
   - Definition: Q2 asking about Index Marking technique, 4 options

3. **Example Question**
   - Term: "Problem: Find duplicates in array [1-n]"
   - Definition: Q3 with manufacturing/factory context example

---

## Integration with Quizlet

### Importing the CSV:
1. Go to Quizlet.com
2. Click "Create" → "Import from Quizlet"
3. Choose "CSV" format
4. Upload `quiz[N]_[topic]_multiple_choice.csv`
5. Select appropriate study settings
6. Create study sets and flashcards

### Organization:
- One CSV file per quiz section
- Multiple files create multiple study sets
- Organize by topic (Algorithms, Data Structures, System Design)
- Track quiz number for progression

---

## Tips for Best Results

- **Comprehensive Coverage**: Ensure all problems are represented with 3 question types
- **Balance Options**: Make distractor options plausible but incorrect
- **Consistency**: Use same style, terminology, and formatting across all questions
- **Real-World Tie-In**: Include practical examples that relate to learner's goals
- **Answer Validation**: Double-check all answers against source material
- **Iteration**: Refine questions based on learner feedback
- **Version Control**: Track dataset versions in Git with clear commit messages

---

## Common Patterns for Question Types

### Big O Questions
- "What is the time complexity for [technique]?"
- "What space complexity does [approach] require?"
- "Which has the best time complexity?"
- "What is the space complexity in worst case?"
- Double newlines used between each question in dataset

### Technique Questions
- "Which [approach] is used for [problem]?"
- "What data structure is required for [problem]?"
- "What technique detects [specific scenario]?"
- "Which approach minimizes space complexity?"
- Double newlines separate questions for clarity

### Example Questions
- "For [use case], what does [technique] do?"
- "In [industry] context, how is [technique] applied?"
- "What real-world problem does [approach] solve?"
- "Which scenario requires [specific algorithm]?"
- Double newlines maintain visual separation

**Card Separation:** All question types use double newlines between cards for optimal display

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluto-atom-4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

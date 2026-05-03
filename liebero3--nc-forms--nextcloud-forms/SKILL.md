---
name: nextcloud-forms
description: Create Nextcloud Forms JSON files from natural language descriptions. Use when the user asks to create a form, survey, questionnaire, or quiz as JSON, or when they provide form descriptions in markdown/text/LaTeX and need them converted to Nextcloud Forms format. Triggers include requests to "create a form", "make a survey", "convert this to Nextcloud Forms JSON", or when form/survey content needs to be imported into Nextcloud. Use when this capability is needed.
metadata:
  author: liebero3
---

# Nextcloud Forms JSON Creator

This skill enables creation of Nextcloud Forms JSON files from natural language descriptions, markdown files, or structured text.

## JSON Structure Overview

```json
{
    "title": "Form Title",
    "description": "Optional form description",
    "share_with_group": "admin",
    "questions": [
        {
            "text": "Question text",
            "type": "question_type",
            "isRequired": true/false,
            "options": ["Option1", "Option2"]  // only for choice types
        }
    ]
}
```

## Question Types Reference

| Type | Description | Has Options | Use Case |
|------|-------------|-------------|----------|
| `short` | Single-line text input | No | Names, short answers |
| `long` | Multi-line text area | No | Essays, detailed responses |
| `multiple` | Multiple checkboxes | Yes | Select multiple items |
| `multiple_unique` | Radio buttons | Yes | Select exactly one item |
| `dropdown` | Dropdown menu | Yes | Select one from many options |
| `date` | Date picker | No | Birthdays, deadlines |
| `time` | Time picker | No | Appointments, schedules |
| `file` | File upload | No | Document submissions |
| `linearscale` | Rating scale | No | Likert scales, ratings |
| `color` | Color picker | No | Color preferences |

## Text to JSON Mapping Rules

### Identifying Question Types

Apply these rules in order to determine the correct type:

1. **Keywords → Type mapping:**
   - "Name", "Email", "ID", "kurz", "short" → `short`
   - "Beschreib", "Erkläre", "Essay", "ausführlich", "lang" → `long`
   - "Mehrfach", "alle zutreffenden", "multiple choice" → `multiple`
   - "Wähle eine", "eine Option", "entweder oder" → `multiple_unique`
   - "Auswahl aus Liste", "Dropdown" → `dropdown`
   - "Datum", "Geburtstag", "date" → `date`
   - "Uhrzeit", "Zeit", "time" → `time`
   - "Datei", "Upload", "Hochladen" → `file`
   - "Bewerte", "Skala", "1-5", "Rating" → `linearscale`
   - "Farbe", "color" → `color`

2. **Context clues:**
   - Multiple possible answers listed → `multiple` or `multiple_unique`
   - Explicit "nur eine Antwort" → `multiple_unique`
   - No explicit restriction → `multiple`
   - Long list (>5 items) → prefer `dropdown`

3. **Default fallback:**
   - If unclear and expects short text → `short`
   - If unclear and expects longer text → `long`

### Required vs Optional

- **Default:** Questions are `isRequired: false` unless explicitly stated
- **Make required when:**
  - Text contains "Pflichtfeld", "erforderlich", "required", "must"
  - Critical information (name, contact in contact forms)
  - Context implies necessity (e.g., "Your name" in signup form)

### Option Extraction

When extracting options from text:

1. **List formats:**
   ```
   - Option A     →  ["Option A", "Option B", "Option C"]
   - Option B
   - Option C
   ```

2. **Inline formats:**
   ```
   (Ja/Nein/Vielleicht)  →  ["Ja", "Nein", "Vielleicht"]
   ```

3. **Common patterns:**
   - Yes/No questions → `["Ja", "Nein"]` or locale-appropriate
   - Agreement scales → `["Stimme voll zu", "Stimme zu", "Neutral", "Stimme nicht zu", "Stimme gar nicht zu"]`
   - Never/Sometimes/Always → `["Nie", "Selten", "Manchmal", "Oft", "Immer"]`

## Validation Rules

Before outputting JSON, verify:

1. **Structure:**
   - Top-level has `title`, `questions` array
   - Optional: `description`, `share_with_group`

2. **Each question has:**
   - `text` (string, not empty)
   - `type` (one of the 10 valid types)
   - `isRequired` (boolean)
   - `options` (array) if type is `multiple`, `multiple_unique`, or `dropdown`

3. **Options array:**
   - Only present for choice types
   - Contains at least 2 items
   - All items are non-empty strings

4. **Common mistakes to avoid:**
   - Don't add `options` to non-choice types
   - Don't forget `isRequired` field (always include it)
   - Don't use invalid type names

## Example Conversions

### Example 1: Simple Contact Form

**Input:**
```
Contact Form

Name (required)
Email address (required)
Your message
```

**Output:**
```json
{
    "title": "Contact Form",
    "description": "",
    "share_with_group": "admin",
    "questions": [
        {
            "text": "Name",
            "type": "short",
            "isRequired": true
        },
        {
            "text": "Email address",
            "type": "short",
            "isRequired": true
        },
        {
            "text": "Your message",
            "type": "long",
            "isRequired": false
        }
    ]
}
```

### Example 2: Survey with Choices

**Input:**
```
Customer Satisfaction Survey

How satisfied are you? (Rate 1-5)
What did you like? (Select all that apply)
- Product quality
- Customer service
- Delivery speed
- Price

Would you recommend us? (Yes/No/Maybe)
```

**Output:**
```json
{
    "title": "Customer Satisfaction Survey",
    "description": "",
    "share_with_group": "admin",
    "questions": [
        {
            "text": "How satisfied are you? (Rate 1-5)",
            "type": "linearscale",
            "isRequired": false
        },
        {
            "text": "What did you like? (Select all that apply)",
            "type": "multiple",
            "isRequired": false,
            "options": [
                "Product quality",
                "Customer service",
                "Delivery speed",
                "Price"
            ]
        },
        {
            "text": "Would you recommend us?",
            "type": "multiple_unique",
            "isRequired": false,
            "options": ["Yes", "No", "Maybe"]
        }
    ]
}
```

### Example 3: Event Registration

**Input:**
```markdown
# Workshop Registration

## Personal Information
- Full Name (Pflichtfeld)
- Date of Birth
- Email (required)

## Workshop Selection
Choose one workshop:
1. Python for Beginners
2. Advanced JavaScript
3. Data Science Basics

## Dietary Requirements
Select all that apply:
- Vegetarian
- Vegan
- Gluten-free
- No restrictions

## Additional Notes
Any other information we should know?
```

**Output:**
```json
{
    "title": "Workshop Registration",
    "description": "",
    "share_with_group": "admin",
    "questions": [
        {
            "text": "Full Name",
            "type": "short",
            "isRequired": true
        },
        {
            "text": "Date of Birth",
            "type": "date",
            "isRequired": false
        },
        {
            "text": "Email",
            "type": "short",
            "isRequired": true
        },
        {
            "text": "Choose one workshop",
            "type": "multiple_unique",
            "isRequired": false,
            "options": [
                "Python for Beginners",
                "Advanced JavaScript",
                "Data Science Basics"
            ]
        },
        {
            "text": "Dietary Requirements (Select all that apply)",
            "type": "multiple",
            "isRequired": false,
            "options": [
                "Vegetarian",
                "Vegan",
                "Gluten-free",
                "No restrictions"
            ]
        },
        {
            "text": "Any other information we should know?",
            "type": "long",
            "isRequired": false
        }
    ]
}
```

## Workflow

1. **Parse input:** Identify form title, questions, and structure
2. **Map questions:** Apply type identification rules for each question
3. **Extract options:** For choice types, carefully extract all options
4. **Set requirements:** Determine `isRequired` based on keywords and context
5. **Validate:** Check structure against validation rules
6. **Output:** Create clean, properly formatted JSON
7. **Save:** Write to `/mnt/user-data/outputs/` with descriptive filename (e.g., `customer_survey.json`)

## Quality Standards

- **Preserve intent:** Don't over-interpret vague descriptions; ask for clarification if needed
- **Clean text:** Remove markdown formatting from question text (no `#`, `*`, etc.)
- **Consistent formatting:** Use proper indentation (4 spaces)
- **German/English:** Handle both languages naturally, preserve the language of questions
- **Comments:** Don't add `_comment` fields unless user explicitly requests them

## Common Pitfalls

❌ **Wrong:** Adding options to text fields
```json
{
    "text": "Your name",
    "type": "short",
    "options": []  // ← Remove this!
}
```

✅ **Correct:**
```json
{
    "text": "Your name",
    "type": "short",
    "isRequired": false
}
```

❌ **Wrong:** Using invalid type names
```json
{"type": "text"}  // ← Should be "short" or "long"
{"type": "checkbox"}  // ← Should be "multiple" or "multiple_unique"
{"type": "select"}  // ← Should be "dropdown"
```

✅ **Correct:**
```json
{"type": "short"}
{"type": "multiple"}
{"type": "dropdown"}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/liebero3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

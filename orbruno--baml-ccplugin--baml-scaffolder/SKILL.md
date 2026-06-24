---
name: baml-scaffolder
description: Automatically create BAML functions, types, and clients when user describes LLM integration needs. Use when user wants to build LLM functionality without knowing BAML syntax. Use when this capability is needed.
metadata:
  author: orbruno
---

# BAML Scaffolder

## When to Use This Skill

Automatically invoke this skill when:
- User describes wanting to extract structured data from LLM
- User mentions building AI features or LLM integrations
- User asks to create functionality that requires LLM calls
- User describes input/output schema for AI processing
- User wants to classify, summarize, extract, or generate content with AI

## Examples That Trigger This Skill

- "I need to extract contact information from emails"
- "Can you help me build a receipt parser?"
- "I want to classify customer feedback into categories"
- "How do I summarize articles using an LLM?"
- "I need to extract structured data from invoices"
- "Create an AI function that analyzes sentiment"

## How to Use

1. **Understand requirements**:
   - What data goes in (text, image, etc.)?
   - What structured output is needed?
   - Which LLM provider to use?
   - Any specific validation or constraints?

2. **Check for BAML project**:
   - Look for `baml_src/` directory
   - If not found, suggest initializing with `/baml-toolkit:init`

3. **Design BAML components**:
   - Output type (class or enum)
   - Input parameters
   - Function signature
   - Prompt template

4. **Generate BAML code**:
   - Create type definitions
   - Write function with proper Jinja prompt
   - Add validation assertions
   - Include test case

5. **Add to BAML files**:
   - Append to appropriate `.baml` file
   - Show created code to user
   - Suggest running `/baml-toolkit:generate`

## BAML Patterns

### Extraction Pattern

When user wants to extract structured data:

```baml
// Define output type
class ContactInfo {
  name string
  email string?
  phone string?
  company string?
}

// Create extraction function
function ExtractContact(text: string) -> ContactInfo {
  client GPT4
  prompt #"
    Extract contact information from the following text.
    Return name, email, phone, and company if mentioned.

    Text: {{ text }}
  "#
}

// Add test
test ExtractContact {
  functions [ExtractContact]
  args {
    text "John Smith - john@example.com - Acme Corp"
  }
  assert {
    {
      checks [
        this.name == "John Smith",
        this.email == "john@example.com"
      ]
    }
  }
}
```

### Classification Pattern

When user wants to categorize content:

```baml
// Define categories
enum SentimentCategory {
  POSITIVE
  NEGATIVE
  NEUTRAL
  MIXED
}

// Create classifier
function ClassifySentiment(text: string) -> SentimentCategory {
  client GPT4
  prompt #"
    Classify the sentiment of this text.
    Choose: POSITIVE, NEGATIVE, NEUTRAL, or MIXED

    Text: {{ text }}
  "#
}
```

### Generation Pattern

When user wants to generate content:

```baml
class GeneratedEmail {
  subject string
  body string
  tone string
}

function GenerateEmail(
  recipient: string,
  purpose: string,
  key_points: string[]
) -> GeneratedEmail {
  client GPT4
  prompt #"
    Generate a professional email.

    Recipient: {{ recipient }}
    Purpose: {{ purpose }}
    Key points to include:
    {% for point in key_points %}
    - {{ point }}
    {% endfor %}

    Return a structured email with subject and body.
  "#
}
```

### Image Analysis Pattern

When user wants to analyze images:

```baml
class ImageAnalysis {
  description string
  objects string[]
  text_detected string?
  scene_type string
}

function AnalyzeImage(image: image) -> ImageAnalysis {
  client GPT4Vision
  prompt #"
    Analyze this image in detail.

    Provide:
    - Overall description
    - List of objects/items visible
    - Any text detected (OCR)
    - Type of scene (indoor/outdoor/document/etc)

    {{ _.role('user') }}
    Image: {{ image }}
  "#
}
```

## Decision Making

**Choose output type:**
- **Enum** if output is a fixed set of categories
- **String/Int/Float** if output is a single primitive value
- **Class** if output is structured with multiple fields
- **Array** if output is a collection of items

**Choose LLM client:**
- **GPT4** for complex reasoning, high accuracy
- **GPT4Vision** for image/video inputs
- **Claude** for long context, coding tasks
- **Gemini** for multimodal, very long context
- **Ollama** for local models, privacy

**Prompt engineering:**
- Be specific about format and requirements
- Include examples in prompt if needed
- Use Jinja variables for dynamic content
- Add role context: `{{ _.role('user') }}`

## Workflow

1. Listen for user describing LLM use case
2. Extract requirements (inputs, outputs, constraints)
3. Design appropriate BAML components
4. Generate well-structured BAML code
5. Add to project files
6. Include test cases
7. Suggest next steps (generate, test, integrate)

## Notes

- Always include test cases for validation
- Use descriptive names for types and functions
- Add `@description` annotations for clarity
- Keep prompts clear and specific
- Suggest appropriate LLM client based on task
- Offer to create multiple variations if requirements are ambiguous

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/orbruno) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

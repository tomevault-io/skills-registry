---
name: brand-voice
description: Analyse brand voice, tone, and content strategy from website text content. Extracts tone dimensions, voice characteristics, vocabulary patterns, CTA style, and language variant (AU/US/UK English). Use when analysing brand tone of voice, creating content guidelines from existing copy, or documenting brand communication style from scraped website text. Use when this capability is needed.
metadata:
  author: imehr
---

# Brand Voice & Tone Analysis

This skill teaches Claude how to analyse brand voice and content strategy from scraped website text. The analysis produces a structured voice profile usable for content creation and brand guidelines.

## Analysis Framework

### Step 1: Content Collection

Categorise all scraped text into:
- **Headings** (h1–h6): Brand messaging hierarchy
- **Body copy**: Communication style and complexity
- **CTAs**: Action language patterns
- **Navigation labels**: Information architecture language
- **Form labels & placeholders**: Instructional tone
- **Footer content**: Legal/formal register
- **Error messages & empty states**: Empathy and helpfulness
- **Microcopy**: Tooltips, badges, status text

### Step 2: Tone Dimension Analysis

Rate each dimension on a 1–10 spectrum with evidence:

| Dimension | Spectrum | What to Look For |
|---|---|---|
| **Formality** | Casual (1) ↔ Formal (10) | Contractions, slang, sentence structure, vocabulary level |
| **Technical depth** | Accessible (1) ↔ Technical (10) | Jargon usage, assumed knowledge, explanation depth |
| **Authority** | Friendly/peer (1) ↔ Authoritative/expert (10) | First person vs. third person, imperative vs. suggestive, credential signals |
| **Urgency** | Calm/patient (1) ↔ Urgent/action-driven (10) | Time pressure language, scarcity signals, CTA directness |
| **Warmth** | Neutral/corporate (1) ↔ Warm/personal (10) | Personal pronouns (you/your), conversational asides, emoji usage |
| **Humour** | Serious (1) ↔ Playful (10) | Wordplay, informal language, unexpected phrasing |

Each rating MUST include:
- The numeric score
- 1–2 specific evidence quotes (each under 14 words)
- Justification for the score

### Step 3: Voice Characteristics

Identify 3–5 defining voice traits. Each trait needs:
- **Trait name** (adjective)
- **Definition** (one sentence)
- **Evidence** (specific quote from the site, under 14 words)
- **Counter-example** (what this brand would NOT say)

Example:
```
Trait: Confident
Definition: States capabilities directly without hedging or qualifying.
Evidence: "The fastest way to build financial infrastructure"
Counter-example: Would NOT say "We think we might be able to help with..."
```

### Step 4: Language Variant Detection

Identify Australian, American, or British English:

| Check | AU/UK | US |
|---|---|---|
| Spelling | colour, analyse, organisation, centre, licence (noun) | color, analyze, organization, center, license |
| Date format | DD/MM/YYYY | MM/DD/YYYY |
| Currency | AUD ($), GBP (£) first | USD ($) first |
| Vocabulary | "whilst", "amongst", "programme" | "while", "among", "program" |

Evidence must cite specific words found on the site.

### Step 5: CTA Pattern Analysis

Collect all CTAs (button text, link text for actions) and analyse:
- **Verb usage**: Start with verb? Which verbs? (Get, Start, Try, Learn, Explore, Build, Join)
- **Personalisation**: "your" vs. generic ("Start your trial" vs. "Start trial")
- **Length**: Word count pattern
- **Urgency**: Time-limited language? ("Now", "Today", "Free")
- **Specificity**: Vague ("Learn more") vs. specific ("See pricing plans")

Document ≥3 CTA examples with pattern categorisation.

### Step 6: Content Guidelines Generation

Produce at least 5 "do" and 5 "don't" guidelines. Each must be:
- **Specific** (not "be clear" but "use sentences under 20 words for feature descriptions")
- **Evidenced** (derived from actual patterns observed)
- **Actionable** (a content writer can follow it immediately)

Example:
```
DO: Lead CTAs with action verbs ("Start building", "Get started", "Explore features")
DON'T: Use passive CTAs ("Click here", "Submit", "More info")
Evidence: 8/10 observed CTAs begin with an active verb.
```

## Output Format

```json
{
  "tone_dimensions": [
    {
      "dimension": "Formality",
      "score": 4,
      "spectrum": "casual ↔ formal",
      "evidence": ["Direct, conversational headings", "Uses contractions throughout"],
      "justification": "Consistent use of 'you' and contractions suggests accessible, peer-level tone"
    }
  ],
  "voice_characteristics": [
    {
      "trait": "Confident",
      "definition": "States capabilities directly without hedging",
      "evidence": "The fastest way to build financial infrastructure",
      "counter_example": "We think we might be able to help"
    }
  ],
  "language_variant": {
    "detected": "American English",
    "confidence": "HIGH",
    "evidence": ["'color' spelling in UI", "'center' in layout text", "USD currency first"]
  },
  "cta_patterns": [
    {
      "text": "Start building",
      "category": "action-verb-lead",
      "verb": "Start",
      "personalised": false,
      "word_count": 2
    }
  ],
  "content_guidelines": {
    "do": ["Lead CTAs with active verbs", "..."],
    "dont": ["Use passive CTA language", "..."]
  },
  "vocabulary": {
    "preferred_terms": ["build", "scale", "infrastructure"],
    "avoided_terms": [],
    "industry_jargon": ["API", "SDK", "webhook"]
  }
}
```

## Validation Criteria (Gate 4 — Voice)

- S-VOI-01: ≥4 tone dimensions rated with evidence
- S-VOI-02: 3–5 voice traits defined with evidence under 14 words each
- S-VOI-03: Language variant detected with evidence
- S-VOI-04: ≥3 CTA examples documented with pattern analysis
- S-VOI-05: ≥5 do's and ≥5 don'ts generated

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/imehr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

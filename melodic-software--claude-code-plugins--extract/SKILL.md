---
name: extract
description: Extract requirements from documents (PDF, Markdown, Word, URLs). Identifies requirement candidates and outputs in pre-canonical format. Use when this capability is needed.
metadata:
  author: melodic-software
---

# Extract Command

Extract requirements from documents for systematic requirement mining.

## Usage

```bash
/requirements-elicitation:extract path/to/document.pdf
/requirements-elicitation:extract path/to/spec.md --domain "authentication"
/requirements-elicitation:extract https://example.com/features --type competitor
/requirements-elicitation:extract ./docs/*.md --mode full-auto
```

## Arguments

| Argument | Required | Description |
|----------|----------|-------------|
| path-or-url | Yes | File path, glob pattern, or URL to extract from |
| --domain | No | Domain name for organizing output files |
| --mode | No | Autonomy mode: `guided`, `semi-auto`, `full-auto` (default: `semi-auto`) |
| --type | No | Document type hint: `spec`, `transcript`, `regulatory`, `competitor`, `auto` |

## Supported Sources

| Source Type | Examples |
|-------------|----------|
| PDF | `document.pdf`, `spec.pdf` |
| Markdown | `readme.md`, `requirements.md` |
| Text | `notes.txt`, `transcript.txt` |
| Word | `document.docx` |
| URL | `https://docs.example.com/api` |
| Glob | `./docs/*.md`, `./specs/**/*.pdf` |

## Workflow

### Step 1: Source Resolution

Parse the input to determine:

- Single file vs. multiple files (glob)
- Local file vs. URL
- Document type (auto-detect or from --type)

### Step 2: Load Document Extraction Skill

Invoke the `requirements-elicitation:document-extraction` skill to load extraction strategies.

### Step 3: Process Each Document

For each document:

1. **Read/Fetch Content**
   - Use Read tool for local files
   - Use WebFetch for URLs

2. **Assess Document**
   - Determine document type if not specified
   - Choose extraction strategy

3. **Extract Requirements**
   - Spawn `document-miner` agent
   - Apply appropriate patterns
   - Capture with source attribution

4. **Categorize and Deduplicate**
   - Assign types and categories
   - Identify duplicates within and across documents

### Step 4: Save Results

Save extraction results to:

```text
.requirements/{domain}/documents/DOC-{filename}-{timestamp}.yaml
```

### Step 5: Report Summary

Display extraction statistics and key findings.

## Examples

### Single PDF Extraction

```bash
/requirements-elicitation:extract ./docs/requirements.pdf --domain "project-x"
```

Output:

```text
Extracting from: requirements.pdf
Document type: Formal Specification
Mode: semi-auto

Processing... [================] 100%

Extraction Complete:
- Total candidates: 45
- Extracted: 38
- Needs review: 7

By Type:
- Functional: 24
- Non-Functional: 10
- Constraints: 4

Saved to: .requirements/project-x/documents/DOC-requirements-20251225.yaml

Review items flagged - run /requirements-elicitation:gaps for details
```

### Multiple Documents with Glob

```bash
/requirements-elicitation:extract ./specs/*.md --mode full-auto
```

Output:

```text
Found 5 documents matching pattern

Processing:
1. api-spec.md .......... 12 requirements
2. user-stories.md ...... 18 requirements
3. constraints.md ....... 5 requirements
4. nfr-spec.md .......... 8 requirements
5. assumptions.md ....... 3 requirements

Total: 46 requirements extracted
Duplicates detected: 4 (consolidated)
Final count: 42 unique requirements

Saved to: .requirements/specs/documents/
```

### URL Extraction (Competitor Analysis)

```bash
/requirements-elicitation:extract https://competitor.com/features --type competitor --domain "competitive-analysis"
```

Output:

```text
Fetching: https://competitor.com/features
Document type: Competitor Analysis

Extraction Complete:
- Features identified: 15
- Converted to requirements: 15
- Confidence: LOW (external observation)

All items flagged for validation.

Saved to: .requirements/competitive-analysis/documents/DOC-competitor-features.yaml

Next: Validate with stakeholders using /requirements-elicitation:interview
```

## Autonomy Modes

### Guided Mode

```text
AI: "I found this potential requirement in Section 2.1:
     'The system shall support up to 1000 concurrent users'

     Should I extract this as a Performance requirement?"

User: "Yes"

AI: "Extracted as REQ-EXT-001 (Performance/Scalability).
     Next candidate..."
```

### Semi-Autonomous Mode

```text
AI: [Processes document section]

    "Completed Section 2. Extracted 8 requirements:
     - 5 Functional
     - 2 Performance
     - 1 Constraint

     2 items flagged for review. Continue to Section 3?"
```

### Fully Autonomous Mode

```text
AI: [Processes entire document]

    "Extraction complete.

     Summary:
     - 34 requirements extracted
     - 6 flagged for review
     - 3 potential duplicates detected

     Results saved. Ready for gap analysis."
```

## Output Format

### Saved YAML Structure

```yaml
extraction_session:
  timestamp: "2025-12-25T14:30:00Z"
  mode: semi-auto
  domain: "{domain}"

sources:
  - file: "requirements.pdf"
    type: specification
    pages: 45
    processed: true

statistics:
  total_candidates: 52
  extracted: 45
  filtered: 7
  needs_review: 8
  duplicates: 3

requirements:
  - id: REQ-EXT-001
    text: "System shall authenticate users via SSO"
    source:
      file: "requirements.pdf"
      location: "Section 3.1, page 8"
    type: functional
    category: security
    confidence: high
    needs_review: false

review_items:
  - id: REQ-EXT-015
    reason: "Vague performance target"
    original: "System should be responsive"
    suggestion: "Define specific response time"

duplicates:
  - group: [REQ-EXT-003, REQ-EXT-022]
    recommended: REQ-EXT-003
    reason: "More specific statement"
```

## Integration

### Follow-Up Commands

```bash
# Check for gaps after extraction
/requirements-elicitation:gaps

# Analyze meeting transcripts
/requirements-elicitation:analyze-transcript ./meetings/kickoff.md

# Consolidate all sources
/requirements-elicitation:discover "{domain}" --sources documents

# Export to specification format
/requirements-elicitation:export --to canonical
```

## Error Handling

### File Not Found

```text
Error: File not found: ./docs/missing.pdf
Suggestion: Check path and try again
```

### Unsupported Format

```text
Error: Unsupported file format: .xyz
Supported: .pdf, .md, .txt, .docx, URLs
```

### URL Fetch Failed

```text
Error: Could not fetch URL: https://example.com/page
Reason: 404 Not Found
Suggestion: Verify URL is accessible
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melodic-software) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

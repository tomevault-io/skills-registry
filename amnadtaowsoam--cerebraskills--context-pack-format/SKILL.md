---
name: context-pack-format
description: Standard format for preparing context for AI - compact, structured, and token-efficient Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Context Pack Format

## Skill Profile

- [ ] DevOps
- [ ] Backend
- [ ] Frontend
- [x] AI-RAG
- [ ] Security Critical

## Overview

Standard format for preparing context for AI - compact, structured, and uses tokens efficiently.

## Why This Matters

- **Token efficiency**: Reduce context size 50-70%
- **Better focus**: AI understands context faster
- **Consistency**: Same format every time
- **Reusability**: Packs can be reused easily

## Core Concepts & Rules

### 1. Basic Structure

```markdown
# Context Pack: [Topic]

## Summary (50 words max)
[One-paragraph overview]

## Key Files
- `auth.ts` (lines 45-60): Token validation
- `db.ts` (lines 120-135): User queries

## Relevant Code
[Code snippets only - no full files]

## Current State
- What works: [list]
- What's broken: [list]
- Goal: [one sentence]
```

### 2. File Reference Format

#### Snippet with Context

```markdown
## From: `path/to/file.ts`
Lines: 45-60
Purpose: Token validation logic

```typescript
export function validateToken(token: string): User | null {
  try {
    const decoded = jwt.verify(token, SECRET);
    return findUserByEmail(decoded.email);
  } catch (error) {
    return null;
  }
}
```

Why included: Shows current implementation with bug
```

#### Multiple Snippets

```markdown
## Authentication Flow

### Step 1: Login (`auth.ts:10-25`)
```typescript
export async function login(email: string, password: string) {
  const user = await findUserByEmail(email);
  if (!user || !await bcrypt.compare(password, user.passwordHash)) {
    throw new Error('Invalid credentials');
  }
  return generateToken(user);
}
```

### Step 2: Token Generation (`auth.ts:30-40`)
```typescript
function generateToken(user: User): string {
  return jwt.sign({ userId: user.id, email: user.email }, SECRET);
}
```
```

### 3. Compression Techniques

#### Remove Boilerplate

```typescript
❌ Bloat (150 tokens):
import { Request, Response, NextFunction } from 'express';
import { validateToken } from './auth';
import { logger } from './logger';
import { config } from './config';

export async function authMiddleware(
  req: Request,
  res: Response,
  next: NextFunction
) {
  try {
    const token = req.headers.authorization?.split(' ')[1];
    if (!token) {
      return res.status(401).json({ error: 'No token' });
    }
    const user = await validateToken(token);
    if (!user) {
      return res.status(401).json({ error: 'Invalid token' });
    }
    req.user = user;
    next();
  } catch (error) {
    logger.error(error);
    res.status(500).json({ error: 'Server error' });
  }
}

✅ Compressed (40 tokens):
```typescript
// authMiddleware: Extract token → validate → attach user to req
// Returns 401 if invalid, 500 on error
const token = req.headers.authorization?.split(' ')[1];
const user = await validateToken(token);
req.user = user;
```

Savings: 73%
```

#### Use Pseudocode

```
❌ Full implementation (200 tokens)

✅ Pseudocode (30 tokens):
```
function processPayment(order):
  1. Validate order amount
  2. Call Stripe API
  3. Update order status
  4. Send confirmation email
  return success/failure
```

Savings: 85%
```

#### Reference by Description

```
❌ Include entire config file (500 tokens)

✅ Reference (20 tokens):
"Database config: PostgreSQL on localhost:5432, pool size 20"

Savings: 96%
```

### 4. Context Hierarchy

#### Level 1: Critical (Always Include)

```
- Bug location (file + line)
- Error message
- Expected vs actual behavior
- Minimal reproduction code
```

#### Level 2: Important (Include if Space)

```
- Related functions
- Recent changes
- Test cases
- Dependencies
```

#### Level 3: Nice-to-Have (Exclude if Tight)

```
- Full file contents
- Documentation
- Comments
- Configuration
```

## Inputs / Outputs / Contracts

### Inputs

- Task or problem description
- Code files and documentation
- Error messages and stack traces
- Current state information
- Goals and requirements

### Outputs

- Structured context pack
- Compressed code snippets
- Token-efficient format
- Clear problem statement
- Actionable information

### Contracts

- **Input Validation**: All inputs must be valid text/code
- **Output Format**: Outputs follow context pack format specification
- **Token Budget**: Context packs respect configured token limits
- **Structure Guarantee**: All context packs follow standard template
- **Compression Ratio**: Achieves 50-70% token reduction target

## Skill Composition
* **Depends on**: [anti-bloat-checklist](../anti-bloat-checklist/SKILL.md), [retrieval-playbook-for-ai](../retrieval-playbook-for-ai/SKILL.md)
* **Compatible with**: [prompt-library-minimal](../prompt-library-minimal/SKILL.md), [skill-generator](../../00-meta-skills/skill-generator/SKILL.md)
* **Conflicts with**: None
* **Related Skills**: [summarization-rules-evidence-first](../summarization-rules-evidence-first/SKILL.md), [intelligence-router](../../00-meta-skills/intelligence-router/SKILL.md)

## Quick Start

### Basic Context Pack Template

```markdown
# Context Pack: [Topic]

## Summary (50 words max)
[One-paragraph overview]

## Key Files
- `file.ts` (lines X-Y): [purpose]
- `file2.ts` (lines X-Y): [purpose]

## Relevant Code
[Code snippets only - no full files]

## Current State
- What works: [list]
- What's broken: [list]
- Goal: [one sentence]
```

### Example Context Pack

```markdown
# Context Pack: User Authentication Bug

## Summary
Login fails for users with special characters in email.
Error occurs in token validation. Need to fix email sanitization.

## Key Files
- `auth.ts` (lines 45-60): validateToken function
- `utils.ts` (lines 12-20): sanitizeEmail helper

## Relevant Code
```typescript
// auth.ts:45-60
export function validateToken(token: string) {
  const decoded = jwt.verify(token, SECRET);
  const email = decoded.email; // ← Bug: no sanitization
  return findUserByEmail(email);
}
```

## Current State
- Works: Normal emails (test@example.com)
- Broken: Emails with + or . (test+1@example.com)
- Goal: Support all valid email characters
```

## Assumptions

- AI models have token limits
- Context needs to be efficient
- Structured format improves comprehension
- Code snippets are sufficient for most tasks
- Token cost is a consideration

## Compatibility

- **AI Models**: GPT-4, Claude, etc.
- **Languages**: All programming languages
- **Context Types**: Code, docs, configurations
- **File Formats**: Markdown, plain text

## Test Scenario Matrix

| Scenario | Input | Expected Output | Verification |
|----------|-------|-----------------|--------------|
| Create pack | Bug description | Structured context pack | Format compliance |
| Compress code | Full file | Snippet only | Token count reduced |
| Add hierarchy | All information | Prioritized content | Critical info first |
| Use template | Task details | Formatted pack | Template followed |

## Technical Guardrails

### Format Requirements

- All context packs MUST start with summary
- All context packs MUST include key files with line numbers
- All code MUST be snippets, not full files
- All context packs MUST include current state

### Compression Requirements

- All boilerplate MUST be removed
- All implementations MUST use pseudocode where appropriate
- All references MUST use descriptions where possible
- All content MUST follow hierarchy levels

### Quality Requirements

- All summaries MUST be under 50 words
- All code snippets MUST be relevant
- All line numbers MUST be accurate
- All goals MUST be one sentence

## Security Threat Model

### Threats Addressed

- **Token waste**: Efficient context format
- **Information overload**: Hierarchy prioritization
- **Context overflow**: Size limits enforced
- **Quality degradation**: Structured format maintains clarity

### Mitigation Strategies

- Use structured templates
- Implement compression techniques
- Follow hierarchy levels
- Monitor token usage
- Validate content relevance

## Domain-Specific Modules

### Context Pack Generator Module

```typescript
export interface ContextPack {
  topic: string;
  summary: string;
  keyFiles: KeyFile[];
  relevantCode: CodeSnippet[];
  currentState: CurrentState;
}

export interface KeyFile {
  path: string;
  lines: string;
  purpose: string;
}

export function createContextPack(
  topic: string,
  files: Map<string, string>,
  currentState: CurrentState
): ContextPack {
  return {
    topic,
    summary: generateSummary(topic, currentState),
    keyFiles: extractKeyFiles(files),
    relevantCode: extractRelevantCode(files),
    currentState,
  };
}
```

### Code Snippet Extractor Module

```typescript
export function extractSnippet(
  content: string,
  startLine: number,
  endLine: number
): string {
  const lines = content.split('\n');
  return lines.slice(startLine - 1, endLine).join('\n');
}

export function compressCode(code: string): string {
  // Remove comments
  let compressed = code.replace(/\/\/.*$/gm, '');
  // Remove empty lines
  compressed = compressed.replace(/^\s*[\r\n]/gm, '');
  return compressed.trim();
}
```

### Token Analyzer Module

```typescript
export function estimateTokens(text: string): number {
  // Approximate token count (4 chars per token)
  return Math.ceil(text.length / 4);
}

export function calculateSavings(before: number, after: number): number {
  return ((before - after) / before) * 100;
}
```

## Release, Rollback & Ops Notes

### Release Process

1. Define context pack format
2. Create templates
3. Implement compression techniques
4. Test with sample contexts
5. Deploy to production
6. Monitor token usage
7. Adjust format as needed

### Rollback Procedure

1. Revert format changes
2. Restore previous context format
3. Monitor quality impact
4. Roll back if necessary

### Operational Procedures

- **Format validation**: Ensure packs follow standard
- **Token monitoring**: Track usage and savings
- **Quality checks**: Verify AI comprehension
- **Template updates**: Improve templates based on feedback

## Code Quality & Documentation

### Format Standards

- Use consistent structure
- Include all required sections
- Follow hierarchy levels
- Use line numbers for references
- Keep summaries concise

### Documentation Requirements

- Document format specification
- Provide examples for each template
- Include compression techniques
- Track token savings
- Document best practices

## Agent Directives & Error Recovery

### Agent Behavior Rules

1. **Always** start with summary (50 words max)
2. **Always** use line numbers for file references
3. **Always** use snippets over full files
4. **Always** follow hierarchy levels
5. **Always** include current state

### Error Recovery Patterns

| Error Type | Detection | Recovery |
|------------|-----------|----------|
| Invalid format | Format check fails | Apply correct template |
| Too large | Token limit exceeded | Apply compression |
| Missing context | AI can't complete | Add critical info |
| Poor quality | AI response degraded | Add more context |

## Agent Prompt Pack

### Context Pack Creation Prompts

```
"Create a context pack for {task} that:
- Starts with 50-word summary
- Lists key files with line numbers
- Includes relevant code snippets only
- Describes current state (works/broken/goal)
- Follows standard format"
```

### Code Snippet Prompts

```
"Extract code snippets for {task} that:
- Uses line numbers for reference
- Shows only relevant code
- Removes boilerplate
- Adds inline context
- Keeps snippets minimal"
```

### Compression Prompts

```
"Compress this context for AI that:
- Removes boilerplate code
- Uses pseudocode where appropriate
- References by description
- Follows hierarchy levels
- Maintains clarity"
```

## Definition of Done

Context pack is complete when:

- [ ] Summary is 50 words or less
- [ ] Key files listed with line numbers
- [ ] Code uses snippets, not full files
- [ ] Current state clearly described
- [ ] Goal is one sentence
- [ ] Format follows template
- [ ] Hierarchy levels respected
- [ ] Token count optimized
- [ ] Quality maintained

## Anti-patterns

1. **Full files**: Including entire files when snippets suffice
2. **No line numbers**: Vague file references
3. **Long summaries**: Over 50 words
4. **Missing state**: No current state description
5. **No hierarchy**: All information at same level
6. **Boilerplate**: Including unnecessary code
7. **No compression**: Full implementations shown
8. **Redundancy**: Repeating information

## Reference Links

- [OpenAI Tokenizer](https://platform.openai.com/tokenizer)
- [Prompt Engineering Guide](https://www.promptingguide.ai/)
- [Context Optimization Best Practices](https://www.anthropic.com/index/prompt-engineering)

## Versioning & Changelog

### v1.0.0 (2025-02-15)
- Initial release of Context Pack Format skill
- Basic structure and format
- File reference formats (snippet with context, multiple snippets)
- Compression techniques (remove boilerplate, use pseudocode, reference by description)
- Context hierarchy (3 levels)
- Template library (bug fix, feature request, code review)
- Best practices
- Summary and guidelines

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amnadtaowsoam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

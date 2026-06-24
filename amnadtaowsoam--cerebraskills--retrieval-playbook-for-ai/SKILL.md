---
name: retrieval-playbook-for-ai
description: Strategies for efficient context retrieval for AI - what to include, what to exclude, and how to prioritize Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Retrieval Playbook for AI

## Skill Profile

- [ ] DevOps
- [ ] Backend
- [ ] Frontend
- [x] AI-RAG
- [ ] Security Critical

## Overview

Playbook for deciding what context to retrieve for AI - select only what's needed, exclude what's irrelevant, and prioritize appropriately.

## Why This Matters

- **Relevance**: Only include what's relevant
- **Token efficiency**: Don't waste tokens on irrelevant information
- **Better results**: AI can focus better
- **Faster**: Less context = faster

## Core Concepts & Rules

### 1. Decision Tree

```
Task received
    ↓
Is it code-related?
    ├─ Yes → Retrieve code files
    └─ No → Skip code
          ↓
Need current state?
    ├─ Yes → Include recent changes
    └─ No → Skip history
          ↓
Need examples?
    ├─ Yes → Include 1-2 examples
    └─ No → Skip examples
          ↓
Assemble context
```

### 2. Retrieval Rules

#### Rule 1: Only Direct Dependencies

```
❌ Include entire codebase
✅ Include only files directly related

Example:
Task: Fix bug in auth.ts
Include:
- auth.ts (the file)
- types.ts (if auth.ts imports it)
- config.ts (if auth.ts uses it)

Don't include:
- unrelated files
- test files (unless debugging tests)
- documentation
```

#### Rule 2: Snippets Over Full Files

```
❌ Include entire 500-line file
✅ Include 20-line relevant function

Example:
Task: Fix validateToken function
Include:
- validateToken function (lines 45-60)
- Related types (5 lines)
- Helper functions used (10 lines)

Total: ~35 lines vs 500 lines
Savings: 93%
```

#### Rule 3: Recent Over Old

```
✅ Last 3 commits
❌ Full git history

✅ Current implementation
❌ Deprecated code
```

#### Rule 4: Examples Only When Needed

```
Include examples when:
✓ New pattern/concept
✓ Complex logic
✓ User explicitly asks

Skip examples when:
✗ Simple CRUD
✗ Standard patterns
✗ Self-explanatory
```

### 3. Context Prioritization

#### Priority 1: Critical (Always Include)

```
- File with bug/feature
- Error messages
- Relevant types/interfaces
- Direct dependencies
```

#### Priority 2: Important (Include if Space)

```
- Related functions
- Configuration
- Recent changes
- Test cases
```

#### Priority 3: Nice-to-Have (Usually Skip)

```
- Full documentation
- Examples
- Comments
- Historical context
```

## Inputs / Outputs / Contracts

### Inputs

- Task or problem description
- Code repository
- File system
- Documentation
- Error messages and stack traces

### Outputs

- Optimized context for AI
- Relevant code snippets
- Prioritized information
- Token-efficient retrieval
- Quality maintained

### Contracts

- **Input Validation**: All inputs must be valid task descriptions
- **Output Format**: Context follows retrieval playbook standards
- **Token Budget**: Retrieved context respects configured limits
- **Relevance Guarantee**: Only directly relevant information included
- **Priority Enforcement**: Critical information prioritized over nice-to-have

## Skill Composition
* **Depends on**: [context-pack-format](../context-pack-format/SKILL.md), [anti-bloat-checklist](../anti-bloat-checklist/SKILL.md)
* **Compatible with**: [prompt-library-minimal](../prompt-library-minimal/SKILL.md), [vector-database](../../04-database/vector-database/SKILL.md)
* **Conflicts with**: None
* **Related Skills**: [summarization-rules-evidence-first](../summarization-rules-evidence-first/SKILL.md), [retrieval-quality](../../61-ai-production/retrieval-quality/SKILL.md)

## Quick Start

### Quick Checklist

```
Before retrieving, ask:
☐ Is this directly related to task?
☐ Can I use a snippet instead of full file?
☐ Is this the most recent version?
☐ Will AI actually use this?
☐ Am I under my token budget?

If any answer is "no", reconsider including it.
```

## Assumptions

- AI models have token limits
- Context needs to be efficient
- Relevance is more important than completeness
- Token cost is a consideration
- Quality must be maintained

## Compatibility

- **AI Models**: GPT-4, Claude, etc.
- **Repositories**: Git, SVN, etc.
- **File Types**: Code, docs, configs
- **Languages**: All programming languages

## Test Scenario Matrix

| Scenario | Input | Expected Output | Verification |
|----------|-------|-----------------|--------------|
| Bug fix | Error message | Relevant code snippet | Token count reduced |
| Feature dev | Feature spec | Similar features + types | Token count reduced |
| Code review | PR diff | Changed files only | Token count reduced |
| Refactoring | File to refactor | File + dependencies | Token count reduced |

## Technical Guardrails

### Retrieval Requirements

- All context MUST be directly relevant
- All context MUST use snippets over full files
- All context MUST be recent and accurate
- All context MUST follow priority levels

### Filtering Requirements

- All irrelevant files MUST be excluded
- All outdated information MUST be excluded
- All redundant information MUST be excluded
- All unnecessary examples MUST be excluded

### Quality Requirements

- All critical info MUST be included
- All context MUST be accurate
- All context MUST be complete for task
- All context MUST be well-organized

## Security Threat Model

### Threats Addressed

- **Token waste**: Efficient retrieval
- **Information overload**: Prioritization
- **Context overflow**: Size limits enforced
- **Quality degradation**: Structured retrieval

### Mitigation Strategies

- Use decision tree for retrieval
- Implement filtering by relevance
- Follow priority levels
- Monitor token usage
- Validate context quality

## Domain-Specific Modules

### Context Retriever Module

```typescript
export interface ContextRetriever {
  retrieve(task: string, repository: string): Promise<ContextPack>;
}

export class SmartRetriever implements ContextRetriever {
  async retrieve(task: string, repository: string): Promise<ContextPack> {
    const files = await this.getFiles(repository);
    const relevant = this.filterByRelevance(files, task);
    const prioritized = this.prioritize(relevant);
    return this.assemble(prioritized);
  }

  private async getFiles(repo: string): Promise<File[]> {
    // Get files from repository
    return [];
  }

  private filterByRelevance(files: File[], task: string): File[] {
    // Filter by relevance score
    return files.filter(f => this.calculateRelevance(f, task) > 0.7);
  }

  private prioritize(files: File[]): File[] {
    // Sort by priority
    return files.sort((a, b) => b.priority - a.priority);
  }

  private assemble(files: File[]): ContextPack {
    // Assemble context pack
    return {
      summary: this.generateSummary(files),
      code: this.extractSnippets(files),
      types: this.extractTypes(files),
    };
  }
}
```

### Relevance Scorer Module

```typescript
export function calculateRelevance(file: File, task: string): number {
  let score = 0;

  // Direct match
  if (file.path.includes(task)) score += 0.5;

  // Recent changes
  if (file.lastModified > sevenDaysAgo) score += 0.2;

  // File size (smaller is better)
  score += Math.max(0, 1 - file.lines / 500);

  return Math.min(1, score);
}
```

### Token Budget Module

```typescript
export interface TokenBudget {
  maxTokens: number;
  usedTokens: number;
  remainingTokens: number;
}

export function checkBudget(context: ContextPack, budget: number): TokenBudget {
  const tokens = estimateTokens(context);
  return {
    maxTokens: budget,
    usedTokens: tokens,
    remainingTokens: budget - tokens,
  };
}
```

## Release, Rollback & Ops Notes

### Release Process

1. Define retrieval strategies
2. Implement filtering logic
3. Create prioritization rules
4. Test with sample tasks
5. Deploy to production
6. Monitor retrieval efficiency
7. Adjust strategies as needed

### Rollback Procedure

1. Revert retrieval changes
2. Restore previous retrieval logic
3. Monitor quality impact
4. Roll back if necessary

### Operational Procedures

- **Retrieval monitoring**: Track token usage
- **Quality checks**: Verify AI comprehension
- **Strategy updates**: Improve based on feedback
- **Budget management**: Enforce token limits

## Code Quality & Documentation

### Retrieval Standards

- Use decision tree for selection
- Filter by relevance and recency
- Prioritize critical information
- Use snippets over full files
- Track token savings

### Documentation Requirements

- Document retrieval strategies
- Provide examples for each rule
- Include prioritization guidelines
- Track token savings
- Document best practices

## Agent Directives & Error Recovery

### Agent Behavior Rules

1. **Always** use decision tree for retrieval
2. **Always** filter by relevance
3. **Always** prioritize critical information
4. **Always** use snippets over full files
5. **Always** track token usage

### Error Recovery Patterns

| Error Type | Detection | Recovery |
|------------|-----------|----------|
| Poor retrieval | AI can't complete task | Add more context |
| Token limit | Context too large | Remove nice-to-have items |
| Quality issue | AI response degraded | Add critical information |

## Agent Prompt Pack

### Retrieval Prompts

```
"Retrieve context for {task} that:
- Uses decision tree for selection
- Filters by relevance and recency
- Prioritizes critical information
- Uses snippets over full files
- Stays under token budget"
```

### Filtering Prompts

```
"Filter files for {task} that:
- Includes only directly related files
- Uses snippets over full files
- Excludes outdated information
- Excludes redundant information
- Prioritizes by relevance"
```

### Prioritization Prompts

```
"Prioritize context for {task} that:
- Puts critical info first
- Includes important info if space allows
- Excludes nice-to-have items
- Follows hierarchy levels
- Maintains quality"
```

## Definition of Done

Retrieval is complete when:

- [ ] Decision tree followed
- [ ] Only direct dependencies included
- [ ] Snippets used over full files
- [ ] Recent information prioritized
- [ ] Examples only when needed
- [ ] Token budget respected
- [ ] Quality maintained
- [ ] Metrics tracked
- [ ] Best practices followed

## Anti-patterns

1. **Kitchen sink approach**: Include everything just in case
2. **No filtering**: Retrieve all files that mention keyword
3. **Full file dumps**: Including entire files when snippets suffice
4. **No prioritization**: All information at same level
5. **Old information**: Including deprecated or outdated code
6. **No examples**: Missing examples when needed
7. **Too many examples**: Including redundant examples
8. **No hierarchy**: Critical and nice-to-have treated equally

## Reference Links

- [OpenAI Tokenizer](https://platform.openai.com/tokenizer)
- [Prompt Engineering Guide](https://www.promptingguide.ai/)
- [Context Optimization Best Practices](https://www.anthropic.com/index/prompt-engineering)

## Versioning & Changelog

### v1.0.0 (2025-02-15)
- Initial release of Retrieval Playbook for AI skill
- Decision tree for retrieval
- Retrieval rules (4 rules)
- Context prioritization (3 levels)
- Retrieval strategies by task type
- Smart filtering (recency, relevance, size)
- Context assembly
- Metrics to track
- Anti-patterns
- Quick checklist

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amnadtaowsoam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

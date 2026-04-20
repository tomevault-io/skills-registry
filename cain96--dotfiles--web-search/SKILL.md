---
name: web-search
description: >- Use when this capability is needed.
metadata:
  author: cain96
---

# Web Search Skill

This skill provides advanced web search functionality using the `gemini` command, designed for complex inquiries requiring current information and comprehensive analysis.

## 🎯 Purpose

Execute web searches to gather current, relevant information addressing user questions. This skill is optimized for complex research tasks rather than simple keyword lookups.

## 🔧 Usage

Execute web searches using the bash script:

```bash
bash scripts/web-search.sh "<search query>"
```

The search query should be phrased naturally to reflect what you want to find.

## 📋 Workflow

After running searches, follow these steps:

1. **Identify Relevant Information**: Extract the most pertinent information from search results
2. **Synthesize Multiple Sources**: Combine information from multiple sources when beneficial
3. **Cite Information Origins**: Always include source URLs and references
4. **Refine Strategy**: If initial results are inadequate, reconsider search strategy with alternative queries

## 🎨 Best Practices

### When to Use This Skill

- Researching current events or recent updates
- Finding documentation for latest library versions
- Investigating error messages and solutions
- Comparing technologies or approaches
- Gathering best practices and recommendations

### Query Formulation

**Clear Questions**: Use explicit language
- ✅ "Please explain Next.js 15's new features"
- ❌ "Next.js 15"

**Source Specification**: Direct queries to specific sources
- ✅ "Find information from official Next.js documentation"
- ✅ "Search Stack Overflow for TypeScript error solutions"

**Response Format**: Request specific output formats
- ✅ "Provide code examples"
- ✅ "Present results in table format"

**Temporal/Conditional Modifiers**: Specify time or difficulty level
- ✅ "Latest React performance optimization techniques for 2025"
- ✅ "Beginner-friendly Python tutorials"

**Analysis Directives**: Request comparisons and evaluations
- ✅ "Compare Vite and Webpack, including pros and cons"
- ✅ "Analyze trade-offs between different state management solutions"

## 🚫 When NOT to Use

- Information available in local codebase
- Questions about code you've already read
- General programming knowledge that doesn't require current information
- Simple fact-checking that can be answered from existing context

## 💡 Tips

- **Be specific**: More detailed queries yield better results
- **Include context**: Mention your use case or constraints
- **Iterate**: Refine queries based on initial results
- **Verify sources**: Cross-reference information from multiple sources
- **Document findings**: Keep track of useful sources for future reference

## 🔍 Example Queries

```bash
# Technical information
bash scripts/web-search.sh "What are the new features in Next.js 15? Include official release notes."

# Library documentation
bash scripts/web-search.sh "How does React Query's useQuery hook work? Provide code examples from official documentation."

# Error resolution
bash scripts/web-search.sh "TypeScript error: Type 'string' is not assignable to type 'number'. Find solutions on Stack Overflow."

# Latest news
bash scripts/web-search.sh "What are Claude AI's latest updates in 2025? Search Anthropic announcements."

# Best practices
bash scripts/web-search.sh "React performance optimization techniques. Include official documentation and community best practices."

# Comparative analysis
bash scripts/web-search.sh "Compare Vite and Webpack build tools. Include advantages, disadvantages, and use case recommendations."
```

## 📚 Related Skills

- **code-review**: Use after implementing solutions found through web search
- **doc-generator**: Document findings and integrate into project documentation
- **typescript-dev**: Apply TypeScript-specific findings to your projects

---

**Note**: This skill requires the `gemini` command to be installed and configured. Ensure you have proper API access and credentials set up.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cain96) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

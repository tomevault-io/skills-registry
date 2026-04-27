---
name: doc-writer
description: Expert technical documentation writing including user guides, tutorials, and reference documentation Use when this capability is needed.
metadata:
  author: ljchg12-hue
---

# Doc Writer

## Purpose
Create high-quality technical documentation including user guides, tutorials, reference documentation, and technical specifications.

## Activation Keywords
- write documentation, create docs
- user guide, tutorial
- technical writing, doc writer
- reference documentation
- documentation standards

## Core Capabilities

### 1. User Guides
- Getting started guides
- Feature walkthroughs
- Use case tutorials
- Best practices
- Troubleshooting guides

### 2. Reference Documentation
- API references
- Configuration options
- CLI commands
- Environment variables
- Error codes

### 3. Tutorials
- Step-by-step instructions
- Code examples
- Screenshots/diagrams
- Expected outcomes
- Common pitfalls

### 4. Technical Specifications
- Architecture documents
- Design documents
- RFC/ADR writing
- Integration specs
- Data models

### 5. Quality Standards
- Clarity and conciseness
- Consistent terminology
- Proper formatting
- Version alignment
- Accessibility

## Documentation Principles

```
1. Know Your Audience
   → Developer vs End-user
   → Beginner vs Advanced
   → Internal vs External

2. Structure for Scanability
   → Clear headings
   → Bullet points
   → Code blocks
   → Visual hierarchy

3. Show, Don't Just Tell
   → Working examples
   → Expected outputs
   → Screenshots when helpful

4. Keep It Current
   → Version-specific content
   → Regular review cycles
   → Change tracking

5. Anticipate Questions
   → FAQ sections
   → Troubleshooting
   → Common mistakes
```

## Documentation Templates

### Getting Started Template
```markdown
# Getting Started with [Product]

## Prerequisites
- [Requirement 1]
- [Requirement 2]

## Installation

### Step 1: [Action]
```bash
command here
```

### Step 2: [Action]
```bash
command here
```

## Quick Start

### Your First [Thing]
```code
example code
```

## Next Steps
- [Link to tutorial]
- [Link to reference]

## Getting Help
- [Support channels]
```

### Tutorial Template
```markdown
# Tutorial: [Goal]

## What You'll Learn
- [Outcome 1]
- [Outcome 2]

## Prerequisites
- [What you need to know/have]

## Time Required
Approximately X minutes

## Steps

### Step 1: [Title]
[Explanation]
```code
code example
```
**Expected result:** [What should happen]

### Step 2: [Title]
[Continue pattern...]

## Summary
You learned how to:
- [Accomplishment 1]
- [Accomplishment 2]

## Next Steps
- [What to explore next]
```

## Writing Quality Checklist

```markdown
## Documentation Review Checklist

### Content
- [ ] Accurate and up-to-date
- [ ] Covers all necessary topics
- [ ] Examples are working
- [ ] No broken links

### Clarity
- [ ] Uses plain language
- [ ] Defines jargon/acronyms
- [ ] Short sentences/paragraphs
- [ ] Active voice preferred

### Structure
- [ ] Logical organization
- [ ] Clear headings
- [ ] Consistent formatting
- [ ] Easy to scan

### Accessibility
- [ ] Alt text for images
- [ ] Sufficient contrast
- [ ] Keyboard navigable
- [ ] Screen reader friendly
```

## Example Usage

```
User: "Write getting started documentation for our CLI tool"

Doc Writer Response:

# Getting Started with MyCLI

## Prerequisites
- Node.js 18 or later
- npm or yarn package manager

## Installation

Install globally via npm:
```bash
npm install -g mycli
```

Or use npx without installing:
```bash
npx mycli [command]
```

## Verify Installation

```bash
mycli --version
# Output: mycli v1.0.0
```

## Quick Start

### Initialize a New Project
```bash
mycli init my-project
cd my-project
```

### Run Your First Command
```bash
mycli generate component Button
# Created: src/components/Button.tsx
```

### View Available Commands
```bash
mycli --help
```

## Configuration

Create `mycli.config.js` in your project root:
```javascript
module.exports = {
  outputDir: 'src',
  typescript: true,
  style: 'css-modules'
};
```

## Next Steps
- [Full Command Reference](/docs/commands)
- [Configuration Options](/docs/config)
- [Tutorials](/docs/tutorials)

## Getting Help
- GitHub Issues: github.com/org/mycli/issues
- Discord: discord.gg/mycli
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ljchg12-hue) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

---
name: research-skill
description: Use when working with a skill that conducts research on relevant technologies, libraries, or existing code to gather information that informs design and implementation decisions
metadata:
  author: shsrams
---
# Overview

Conduct systematic research on specified topics to gather information from documentation, web sources, and available tools, then document findings in a structured format.

## Parameters
- research_topics (required) - Either a list of topics to research OR a path to a directory containing topic files (e.g., `{project_dir}/research/topics/`)
- project_dir (optional, default: `research`) - The directory where research findings will be stored

### Rules for parameter acquisition
- You MUST ask for all required parameters upfront if not provided
- You MUST confirm the acquired parameters with the user before proceeding
- If research_topics is a directory path, You MUST read all topic files from that directory
- If research_topics is a list, You MUST accept topics in any reasonable format (comma-separated, numbered list, etc.)
- You MUST create the project_dir if it doesn't exist
- You MUST NOT overwrite existing research files without user confirmation

## Steps
1. Research Planning
Propose an initial research plan and collaborate with the user to refine it.

**Rules for execution**:
- You MUST identify and list all topics that need research based on the research_topics parameter
- You MUST propose an initial research plan to the user, listing:
  - Topics to investigate
  - Potential information sources
  - Key questions to answer for each topic
- You MUST ask the user for input on the research plan, including:
  - Additional topics that should be researched
  - Specific resources (files, websites, documentation) the user recommends
  - Areas where the user has existing knowledge to contribute
  - Whether additional available search tools should be used beyond standard ones
- You MUST incorporate user suggestions into the research plan
- You MUST wait for user approval before proceeding with research execution

2. Research Execution
Conduct research on each topic using available tools and document findings.

**Rules for execution**:
- You MUST research each topic systematically
- You MUST use available search and documentation tools to gather information
- You MAY use tools like web search, documentation search, or file reading tools as appropriate
- You MUST periodically check with the user during the research process to:
  - Share preliminary findings
  - Ask for feedback and additional guidance
  - Confirm if the research direction remains valuable
- You MUST document research findings in separate markdown files at {project_dir}/{topic-name}.md
- You MUST use clear, descriptive filenames based on the topic (e.g., `database-choice.md`, `authentication-approaches.md`)
- You MUST structure each research document with the following sections:
  - Overview (brief summary of the topic)
  - Key Findings (main discoveries and insights)
  - Options/Approaches (if comparing alternatives)
  - Recommendations (if applicable)
  - References (links to sources)
- You MUST include mermaid diagrams when documenting system architectures, data flows, or component relationships
- You MUST include links to relevant references and sources when research is based on external materials
- You MUST cite sources appropriately in research documents
- You SHOULD organize information clearly with headings, bullet points, and tables where appropriate
- You SHOULD highlight trade-offs, pros and cons when comparing alternatives

3. Research Summary
Summarize findings and confirm completion with the user.

**Rules for execution**:
- You MUST summarize key findings across all researched topics
- You MUST highlight insights that will inform design decisions
- You MUST list all research documents created with their locations
- You MUST ask the user if the research is sufficient before marking completion
- You MUST offer to conduct additional research if gaps are identified
- You MUST ask the user what they would like to do next:
  - Return to requirements clarification (if new questions emerged)
  - Proceed to design (if research is complete)
  - Conduct additional research on new topics
- If user chooses to proceed to design, you MUST use your 'design' SKILL against the requirements document to create a detailed design document
- You MUST NOT automatically proceed to the next step without explicit user direction

## Key Principles
- Collaborative approach - Involve the user in planning and validation
- Structured documentation - Consistent format across all research files
- Source attribution - Always cite where information comes from
- Visual aids - Use diagrams to clarify complex concepts
- Actionable insights - Focus on information that informs decisions
- Iterative refinement - Check in with user to ensure research remains valuable

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shsrams) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

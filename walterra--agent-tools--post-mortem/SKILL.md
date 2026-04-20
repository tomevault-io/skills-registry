---
name: post-mortem
description: Analyze chat history to identify successes, failures, and improvement opportunities. Generates actionable recommendations for updating project rules, skills, and system prompts. Use when the user asks for a post-mortem, retrospective, session analysis, or wants to improve agent configuration based on past interactions. Use when this capability is needed.
metadata:
  author: walterra
---

# Post-Mortem Analysis

Analyze chat histories to identify successes, failures, and improvement opportunities. Generate actionable recommendations for updating project configuration files (`.cursorrules`, skills, etc.) to prevent similar issues.

## When to Use

- User asks for a "post-mortem" or "retrospective"
- User wants to analyze what went well or wrong in a session
- User wants to improve agent behavior based on past interactions
- User provides a chat export file or URL for analysis

## Input Sources

The analysis can work with:

1. **Current session**: Analyze the ongoing conversation (default if no file provided)
2. **File path**: Chat export in JSON, markdown, or text format
3. **URL**: Link to a shared chat export

## Process

### Phase 1: Analysis

1. **Load and analyze chat**:
   - If no file specified: Analyze the current conversation history
   - If file path provided: Read the chat export file
   - If URL provided: Fetch the chat export using WebFetch
   - Parse the conversation flow and identify key interactions
   - Extract tool usage patterns and decision points
   - Note any error messages, confusion, or repeated attempts

2. **Categorize interactions**:
   - **Successful patterns**: What worked well and why
   - **Failed patterns**: What went wrong and root causes
   - **Missed opportunities**: Where the agent could have been more effective
   - **User friction points**: Where the user had to provide additional guidance

### Phase 2: Assessment

1. **Skill usage review**:
   - Identify skills that were (or should have been) applied
   - Locate and read skill files in `.cursor/skills/` or `~/.cursor/skills/`
   - Assess whether skill instructions were clear and complete

2. **Identify root causes**:
   - Missing context in project rules or skills
   - Unclear or ambiguous instructions
   - Missing skills or workflow guidance
   - Tool selection issues
   - Architecture understanding gaps

3. **Pattern analysis**:
   - Recurring mistakes or confusion
   - Successful strategies that should be reinforced
   - Dependencies that weren't clear
   - Workflow steps that were skipped or misunderstood

### Phase 3: Recommendations

1. **Project rules improvements** (`.cursorrules`):
   - Missing architectural context to add
   - Workflow patterns to emphasize
   - Common pitfalls to warn about
   - Examples that would clarify usage

2. **Skill enhancements**:
   - New skills that should be created
   - Existing skills that need updates
   - Additional constraints needed
   - Process clarifications
   - Tool usage guidelines

### Phase 4: Implementation

1. **Present findings**:
   - Summary of what went well
   - Key issues identified
   - Specific recommendations with rationale
   - Proposed changes to configuration files

2. **User confirmation**:
   - **STOP**: Present findings and ask "Review these recommendations. Proceed with updating files? (y/n)"
   - Allow user to modify or reject specific recommendations

3. **Apply improvements**:
   - Update `.cursorrules` with approved changes
   - Modify or create skills as needed
   - Document changes made

## Example Interactions

**Analyze current session:**
> "Do a post-mortem on this chat"

1. Review current conversation history
2. Identify issues or missed opportunities
3. Present recommendations
4. Get confirmation before updating files

**Analyze external chat:**
> "Post-mortem this chat export ~/Downloads/session.json - focus on the build process issues"

1. Read the chat export file
2. Focus analysis on build-related interactions
3. Identify missing documentation or skills
4. Present recommendations
5. Get confirmation before updating files

## Output Checklist

- [ ] **Analysis report**: What went well vs. what went wrong
- [ ] **Root cause analysis**: Why issues occurred
- [ ] **Actionable recommendations**: Specific file changes to prevent recurrence
- [ ] **Updated configuration**: Improved rules and skills (after user approval)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/walterra) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->

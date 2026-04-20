---
name: investigate
description: Investigate a JIRA ticket, research the codebase, iterate on a plan, and write todos into the ticket file Use when this capability is needed.
metadata:
  author: tomdaly
---

# 🔍 Investigate and plan a ticket

$ARGUMENTS

## context

- The ticket file lives at `~/vault/projects/tickets/<ticketName>.md`
  - If it does not exist, create it using the template at `~/vault/resources/templates/ticket-template.md`
- Use plan mode thinking — do not edit source code during this skill
- Use MCP servers for additional context: atlassian (JIRA), github (PRs/code), notion (internal docs), context7 (library docs)

## steps

### 1. gather context
- Read the JIRA ticket (use atlassian MCP) for acceptance criteria, description, comments
- Search for related PRs, recent commits, and discussions (use github MCP)
- Check for related team context in notion if relevant
- Write findings into the `## context` section of the ticket file

### 2. copy acceptance criteria
- Copy acceptance criteria from the JIRA ticket into the ticket file `## acceptance criteria` section
- If no acceptance criteria exist, draft them based on the ticket description and ask the user to confirm

### 3. STOP — gather personal context
- Ask the user if they have any additional context, constraints, or preferences
- Wait for their input before continuing

### 4. investigate
- Research the codebase to understand the problem space
- Identify relevant files, patterns, and dependencies
- Check for similar past implementations or related code
- Note any risks, edge cases, or dependencies on other teams/systems
- Write findings into the `## investigation` section of the ticket file

### 5. write implementation plan
- Think at a **staff engineer level** — consider project/future scope, not just ticket scope
- Consider: will this approach scale? does it create tech debt? does it conflict with ongoing work?
- Break the plan into logical, atomic steps
- Each step should be independently committable
- STOP during writing to iterate with the user:
  - Present open questions and wait for answers
  - Provide suggestions where multiple approaches exist
  - Do not finalise the plan until all open questions are resolved
- Write the plan into the `## implementation plan` section

### 6. create todos
- Convert the implementation plan into a checkbox list in `## to do`
- Each todo should map to roughly one commit
- Arrange in implementation order, not importance order
- Follow TDD ordering: test first, then implementation, then refactor

### 7. review
- Re-read the full ticket file
- Verify: do the todos fully resolve the acceptance criteria?
- Check for inconsistencies between plan and todos
- Flag anything that looks incomplete or risky
- Present a final summary to the user

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tomdaly) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

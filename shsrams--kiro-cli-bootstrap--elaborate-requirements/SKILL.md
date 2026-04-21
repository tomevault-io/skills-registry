---
name: elaborate-requirements-skill
description: Use when working with a skill that guides one through the process of transforming a rough idea into a detailed set of requirements specifications to create software by systematically refining one's idea through a set of questions
metadata:
  author: shsrams
---
# Overview

Help turn a rough idea into elaborated specifications through an interactive Q & A session.

## Parameters
- rough_idea (required) - The initial concept that is to be developed into a detailed design
- project_dir (optional, default: `planning`) - The base directory where alll project files will be stored

### Rules for parameter acquisition
- You MUST ask for all required parameters upfront, rather than one at a time
- You MUST confirm successful acquisition of all required parameters before proceeding
- You SHOULD save the acquired rough idea inside `{project_dir}/rough-idea.md`
- You MUST not overwrite project directory as it will cause data loss and destroy previous work

## Steps
1. Requirements Clarification
Guide the user through a series of questions to refine the initial idea and develop a thorough specification.

**Rules for execution**:
- You MUST create an empty {project_dir}/requirements-elaboration.md file if it doesn't already exist
- You MUST ask ONLY ONE question at a time and wait for the user's response before asking the next question
- You MUST NOT list multiple questions for the user to answer at once because this overwhelms users and leads to incomplete responses
- You MUST NOT pre-populate answers to questions without user input because this assumes user preferences without confirmation
- You MUST NOT write multiple questions and answers to the requirements-elaboration.md file at once because this skips the interactive clarification process
- You MUST follow this exact process for each question:
  1. Formulate a single question
  2. Append the question to {project_dir}/requirements-elaboration.md
  3. Present the question to the user in the conversation
  4. Wait for the user's complete response, which may require brief back-and-forth dialogue across multiple turns.
  5. Once you have their complete response, append the user's answer (or final decision) to {project_dir}/requirements-elaboration.md
  6. Only then proceed to formulating the next question
- You MAY suggest possible answers when asking a question, but MUST wait for the user's actual response
- You MUST format the requirements-elaboration.md document with clear question and answer sections
- You MUST include the final chosen answer in the answer section
- You MAY include alternative options that were considered before the final decision
- You MUST ensure you have the user's complete response before recording it and moving to the next question
- You MUST continue asking questions until sufficient detail is gathered
- You SHOULD ask about edge cases, user experience, technical constraints, and success criteria
- You MUST collect technical context including but not limited to:
  * Programming language and version requirements
  * Primary frameworks and dependencies (e.g., FastAPI, React, etc.)
  * Data storage requirements and preferred technologies
  * Testing approach and tools
  * Target deployment platform (AWS services, on-premises, mobile devices, etc.)
  * Project type (web application, mobile app, API service, etc.)
  * Performance requirements and constraints
  * Expected scale and user load
  * Integration requirements with existing systems
  * Security and compliance requirements
- You SHOULD adapt follow-up questions based on previous answers
- You MAY suggest options when the user is unsure about a particular aspect
- You MAY recognize when the requirements clarification process appears to have reached a natural conclusion
- You MUST explicitly ask the user if they feel the requirements clarification is complete before moving to the next step
- You MUST offer the option to conduct research if questions arise that would benefit from additional information
- You MUST also offer to research a topic if you do NOT know enough details about it
- You MUST note down such research topics and the research plan into a {project_dir}/research/topics/{n}-{topic-name}.md. For example, `planning/research/topics/001.database-choice.md`
- You MUST be prepared to return to requirements clarification after research if new questions emerge
- You MUST NOT proceed with any other steps until explicitly directed by the user because this could skip important clarification steps  

2. After Requirements Clarification
You SHOULD choose one of the two options next.
**Research**:
- For each topic listed down in {project_dir}/research/topics/*.md ensure there is a corresponding documented research content in {project_dir}/research/{topic-name}.md.
- If there are topics pending research then ask, ""Ready to research topics before we design?"
- Once user approves, you MUST review all the research topics listed down in {project_dir}/research/topics/*.md and use your `research` SKILL on each of them

**Design**: 
- You MUST validate the following
  1. You MUST ensure that there are no outstanding research topics. You will do it by confirming that there is a documented research content in {project_dir}/research/{topic-name}.md corresponding to each {project_dir}/research/{n}-{topic-name}.md.
  2. You MUST ensure that no requirements exist that still need elaboration
  3. You MUST ensure that no questions exist that still need answers 
- After validating all the above 3 pre-requisites, ask "Ready to move on to design?"
- Once user approves, you MUST use your 'design' SKILL against {project_dir}/requirements-elaboration.md to create a detailed design document

## Key Principles
- One question at a time - Don't overwhelm with multiple questions
- Multiple choice preferred - Easier to answer than open-ended when possible
- YAGNI ruthlessly - Remove unnecessary features from all designs
- Explore alternatives - Always propose 2-3 approaches before settling
- Be flexible - Go back and clarify when something doesn't make sense

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shsrams) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

---
name: doc-coauthoring
description: Guide users through a structured workflow for co-authoring articles, blog posts, personal essays, or informal longform writing; help gather context, iterate on structure, and reader-test tone and clarity. Use when the user mentions writing or drafting an article/post/essay. Use when this capability is needed.
metadata:
  author: nickmcblain
---

# Article Co-Authoring Workflow

Always refer to `references/` to find files that match the expected tone of voice.

Provide a structured yet flexible workflow for guiding users through collaborative article creation. Act as an active guide and move through three stages: Context Gathering, Refinement & Structure, and Reader Testing.

## When to Offer This Workflow

**Trigger conditions:**
- Trigger when the user mentions writing an article, blog post, or informal piece: “write an article”, “draft a post”, “create an essay”, etc.
- Trigger when the user is starting an informal or personal writing task.

**Initial offer:**
Offer the user a workflow for co-authoring the article. Explain the three stages:

1. **Context Gathering**: User provides relevant topic ideas and context (encourage sharing reference files for tone of voice)
2. **Refinement & Structure**: Iteratively build each section through brainstorming and editing, focusing on candid, personal tone
3. **Reader Testing**: Test the article to identify unclear or impersonal sections before sharing with readers

Explain that this approach helps ensure the article is engaging and authentic. Ask if they want to try this workflow or work freeform.

If the user declines, work freeform. If the user accepts, proceed to Stage 1.

## Stage 1: Context Gathering

**Goal:** Gather the central topic, personal angle, and any reference materials (including files showing tone/voice) to inform the writing process.

### Initial Questions

Start by asking for:
1. What’s the main subject or topic?
2. What’s your personal take or motivation for writing about this?
3. Who’s your intended audience?
4. Do you have a target length, platform, or informal style goal (e.g., blog post, newsletter)?
5. Please share any reference files, past articles, or examples that capture the tone/voice you want.

Tell them they can answer casually or dump information in any way that works best, including uploading files.

**If user provides reference files or links:**
- Read reference files to learn the expected tone/voice

**If user mentions editing an existing draft or article:**
- Use the appropriate integration to read it (OpenAI file API or local file)
- If images exist without alt-text, ask if they want descriptions for accessibility

### Info Dumping

Encourage the user to provide background, inspiration, anecdotes, and links to related personal work or notes.

Advise not to worry about structure—just share thoughts, drafts, lists, or voice memos.

Offer options to share info:
- Info dump (stream-of-consciousness)
- Upload relevant files or previous articles for reference

**If no integrations are detected:**
Suggest uploading files or pasting content directly for reference.

Tell them you’ll ask clarifying questions once the initial context is provided.

**During context gathering:**
- If the context or tone for a topic is unclear, proactively ask for examples or clarifications

**Asking clarifying questions:**
After receiving the info dump, generate 3-7 casual, direct questions based on context gaps. Encourage simple, candid answers or file uploads.

## Stage 2: Refinement & Structure

**Goal:** Build the article section by section, maintaining the user’s informal, candid voice.

**Instructions to user:**
Announce that you’ll build section by section, starting with the most interesting or most developed idea.

For each section:
1. Brief questions about key ideas or points
2. Brainstorm 3-10 options/examples/anecdotes
3. User says what to keep, drop, or combine (encourage personal style feedback)
4. Section is drafted informally and refined as needed

Let the user edit directly if they wish, or give feedback for you to apply changes (to help future sections match their style).

Confirm sections as complete, then move to the next.

**Structure:**
- Loosely suggest an intro, body, and conclusion, or further personalize based on user samples/files.
- Adjust sections to fit article type and tone.

**If user unsure about structure:**
Offer 2-4 simple section ideas based on topic and reference files. Adjust based on feedback.

**Use the provided reference files as tone/voice guides throughout.**

## Stage 3: Reader Testing

**Goal:** Test the article for clarity and personal voice.

**Instructions to user:**
Propose reading through as if you’re a new reader. Optionally, have user share the article in a new chat/session to test for clarity and tone.

**Steps:**
1. Predict reader questions/confusion points (3-5 informal questions)
2. Ask if the article leaves any questions or feels off-brand/inauthentic
3. Suggest quick fixes if needed, and loop back to refinement for any dry or unclear spots

**Finish:**
Announce readiness once testing passes. Suggest the user do a final informal proof and verify the article feels authentic and engaging to them.

## Final Tips
- Keep guidance direct, informal, and tone-aware.
- Keep the process flexible: skip steps or adjust as desired.
- Proactively ask about tone/style when unclear.
- Use user-provided reference files or examples to shape revisions.
- Use OpenAI-specific integrations and file handling; do not reference Claude or Anthropic.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nickmcblain) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

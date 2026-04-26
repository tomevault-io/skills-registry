---
name: meeting-notes
description: "Use this skill to automatically generate comprehensive meeting minutes, including key decisions, action items, and follow-up tracking. Triggers: meeting notes, meeting minutes, meeting summary, action items, decisions, follow-up, ata de reunião, resumo da reunião, pauta da reunião."
allowed-tools: [Read, Write, Edit, Bash, Browser]
license: MIT License
metadata:
    skill-author: Lucas Kefler Bergamaschi
---

# Meeting Notes Automation

## Overview
This skill is designed to streamline the process of documenting meetings. It takes a raw meeting transcript or a set of rough notes and transforms them into a structured, professional, and actionable summary. The primary goal is to save time, improve clarity, and ensure that all critical outcomes of a meeting, such as decisions and action items, are captured and tracked effectively. By automating this process, teams can focus more on the meeting's content and less on the administrative burden of note-taking.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: meeting notes, meeting minutes, meeting summary, action items, decisions, follow-up, transcript, ata de reunião, resumo da reunião, pauta da reunião, minuta da reunião.
- Phrases: "generate meeting notes", "summarize the meeting", "create meeting minutes from this transcript", "what were the action items?", "document the decisions", "gerar ata da reunião", "resumir a reunião", "quais foram os itens de ação?".
- Context: Any discussion about documenting the outcomes of a meeting, processing a meeting transcript, or creating a structured summary of a discussion.

**Example user queries that trigger this skill:**
- "Can you summarize this meeting transcript for me?"
- "I need to create the meeting minutes for the project sync."
- "Please extract the action items and decisions from our last call."
- "Preciso de um resumo da nossa reunião de ontem."
- "Gere a ata da reunião a partir desta transcrição."

## When to Use This Skill
This skill is particularly useful in the following scenarios:

- **Post-Meeting Documentation:** After a virtual or in-person meeting, use this skill to process the audio transcript (e.g., from Zoom, Google Meet) or your handwritten notes.
- **Formal Record Keeping:** When you need to create a formal record of a meeting for compliance, project management, or corporate governance purposes.
- **Team Alignment:** To quickly share a clear and concise summary with all attendees and stakeholders, ensuring everyone is aligned on the outcomes.
- **Action Item Tracking:** When a meeting generates a list of tasks, this skill helps in clearly defining them, assigning owners, and setting deadlines.
- **High-Stakes Meetings:** For important meetings like board meetings, project kick-offs, or client negotiations, where accurate documentation is crucial.
- **Recurring Meetings:** To maintain a consistent format and historical record of recurring team meetings, such as weekly syncs or monthly reviews.

## Core Capabilities

### 1. Transcript Summarization
- **Intelligent Condensing:** The skill analyzes a full meeting transcript and extracts the most important points, discussions, and decisions.
- **Topic Detection:** It automatically identifies the main topics discussed during the meeting.
- **Speaker Identification:** When provided with speaker labels, the skill can attribute key points to the correct individuals.

### 2. Key Outcome Extraction
- **Decision Logging:** Automatically identifies and lists all key decisions made during the meeting. It clarifies what was decided and why.
- **Action Item Identification:** Detects phrases that indicate a task or action is being assigned. It captures the task description, the assigned person (owner), and the due date if mentioned.
- **Question & Open Point Highlighting:** Flags unresolved questions or topics that require further discussion or follow-up.

### 3. Structured Document Generation
- **Template-Based Formatting:** Uses a clean and professional template to structure the meeting notes, which can be customized.
- **Multiple Output Formats:** While the primary output is Markdown, the content can be easily converted to PDF, HTML, or copied into other systems like Confluence, Notion, or email.
- **Custom Sections:** Users can define custom sections to be included in the minutes, such as "Risks Identified" or "Next Meeting Agenda."

### 4. Follow-up and Tracking
- **Action Item Table:** Generates a clear, tabular summary of all action items, including columns for the task, owner, due date, and status.
- **Follow-up Reminders:** The structured output makes it easy to integrate with task management systems (e.g., Jira, Asana, Trello) to create tasks and set reminders.

## Step-by-Step Workflow

### Step 1: Prepare Your Input
Your input can be a raw text file containing either:
- A full meeting transcript from a service like Zoom, Otter.ai, or Google Meet.
- Your own unstructured notes taken during the meeting.

**Example Raw Input (transcript.txt):**
```
Sarah: Okay, team, let's kick off the Project Phoenix sync. First on the agenda is the Q3 marketing campaign. John, how are the creatives coming along?

John: Hi Sarah. The design team has finalized the visuals. We're on track. I think we need to decide on the final copy for the social media posts by this Friday.

Lucas: I can take the lead on drafting the copy. I'll have a draft ready for review by Wednesday EOD.

Sarah: Perfect, Lucas. Thanks. So, the decision is to finalize the copy by Friday. Lucas to provide the draft. Next, the budget. We have an overspend of 10% in our cloud hosting. We need a plan to mitigate this.

Maria: I analyzed the usage reports. The staging environment is running 24/7. We can implement a script to shut it down during weekends. That should save us about 15%.

Sarah: Great idea, Maria. Please go ahead and implement that. Can you get it done by the end of next week?

Maria: Yes, I can.

Sarah: Okay, action item for Maria. Anything else? No? Okay, meeting adjourned.
```

### Step 2: Invoke the Skill
Use the `file` tool to create the input file and then invoke the skill with a clear prompt.

**Example Command:**
```bash
# 1. Create the input file
file.write(
    path="/home/ubuntu/project_phoenix/transcript.txt",
    text="[...paste the raw transcript here...]"
)

# 2. Run the skill (conceptual)
# This is a representation of how a user would ask Manus to run the skill.
# "Manus, please process the meeting transcript at /home/ubuntu/project_phoenix/transcript.txt and generate the meeting notes."
```

### Step 3: Define the Output Structure (Agent's Internal Prompt)
As the agent executing this skill, you will use a prompt that guides the transformation. This is the core logic of the skill.

**Internal Prompt Template:**
```
From the following meeting transcript located at `[path/to/transcript.txt]`, generate a structured meeting minutes document. The document must contain the following sections:

1.  **Meeting Details:**
    *   **Meeting Title:** [Infer from context, e.g., Project Phoenix Sync]
    *   **Date:** [Current Date]
    *   **Attendees:** [List of speakers, e.g., Sarah, John, Lucas, Maria]

2.  **Executive Summary:** A brief, one-paragraph summary of the meeting's purpose and key outcomes.

3.  **Key Decisions:** A bulleted list of all significant decisions made.

4.  **Action Items:** A Markdown table with the columns: `Action Item`, `Assigned To`, `Due Date`, `Status`.

5.  **Discussion Summary:** A more detailed summary of the main topics discussed, organized by topic.

Format the final output as a single Markdown file named `meeting_notes.md`.
```

### Step 4: Review and Refine the Output
The skill will produce a Markdown file. Review it for accuracy and completeness.

**Example Output (meeting_notes.md):**
```markdown
# Meeting Minutes: Project Phoenix Sync

**Date:** 2026-02-02
**Attendees:** Sarah, John, Lucas, Maria

## Executive Summary

The team reviewed the progress of the Q3 marketing campaign and addressed a budget overspend in cloud hosting. Key decisions included finalizing the social media copy by this Friday and implementing a cost-saving script for the staging environment. Action items were assigned to Lucas and Maria to execute these tasks.

## Key Decisions

- The final copy for the Q3 marketing campaign's social media posts will be finalized by this Friday.
- A script will be implemented to shut down the staging environment on weekends to reduce cloud hosting costs.

## Action Items

| Action Item                                               | Assigned To | Due Date          | Status      |
| --------------------------------------------------------- | ----------- | ----------------- | ----------- |
| Draft the social media copy for the Q3 marketing campaign. | Lucas       | Wednesday, EOD    | Not Started |
| Implement script to shut down staging environment on weekends. | Maria       | End of next week  | Not Started |

## Discussion Summary

### Q3 Marketing Campaign

John confirmed that the creative visuals for the campaign are complete and the team is on schedule. A discussion was held regarding the need to finalize the ad copy. Lucas volunteered to draft the initial version for the team to review, with a final decision to be made by Friday.

### Cloud Budget Overspend

Sarah highlighted a 10% overspend in the cloud hosting budget. Maria presented an analysis of the usage reports, identifying that the staging environment was a primary contributor to the excess cost. She proposed a solution to automatically shut down the environment during non-working hours (weekends), which was approved. This is expected to result in a cost saving of approximately 15%, effectively mitigating the overspend.
```

## Best Practices

- **Provide Clean Input:** For best results, provide a clean transcript. Remove filler words (`um`, `ah`) or irrelevant side conversations if possible before feeding it to the skill.
- **Use Speaker Labels:** Transcripts with clear speaker labels (e.g., `Speaker 1:`, `John Doe:`) produce more accurate and attributable notes.
- **Be Specific in Your Request:** When interacting with Manus, specify any particular focus. For example, "Pay special attention to the budget discussion" or "Ensure all deadlines are captured."
- **Review and Edit:** AI is a powerful assistant, but not infallible. Always perform a quick review of the generated notes to correct any misinterpretations or add nuances the AI may have missed.
- **Integrate with Other Tools:** Use the output of this skill as an input for other workflows. For example, use the `Bash` tool to pipe the action items into a script that creates tasks in Jira or sends a summary email.

## Examples

### Example 1: Formal Board Meeting

- **Input:** A 2-hour audio transcript from a quarterly board meeting.
- **Prompt:** "Generate the official minutes for the Q1 Board Meeting from the attached transcript. Ensure the format is formal and includes a section for `Motions and Votes`."
- **Output:** A detailed document with an executive summary, a list of all motions, who proposed and seconded them, the outcome of the votes, a summary of the financial review, and a list of high-level strategic action items assigned to the executive team.

### Example 2: Agile Sprint Planning

- **Input:** Rough notes from a Jamboard session and a list of discussed user stories.
- **Prompt:** "Create a summary of our sprint planning meeting. The input is in `sprint_notes.txt`. List the user stories committed for this sprint and any identified dependencies or risks."
- **Output:** A concise summary including the sprint goal, a list of committed stories, a table of dependencies, and a section on potential risks that were flagged by the team.

### Example 3: Client Kick-off Call

- **Input:** A transcript from a Zoom call with a new client.
- **Prompt:** "Process the client kick-off call transcript. I need a summary to share internally. Focus on client expectations, project scope, and any immediate next steps we promised."
- **Output:** A summary focused on the client's perspective, clearly outlining their stated goals, the agreed-upon project scope, a list of "quick wins" to build momentum, and action items for the account manager and project lead.

## Templates for Use

Here are a few templates you can use as a starting point for your internal prompts to guide the skill.

### Template 1: Standard Team Meeting

```
Generate meeting minutes from the text in `[source_file.txt]`.

**Meeting Title:** [Specify Title]
**Date:** [Specify Date]
**Attendees:** [List Attendees]

**Sections to Include:**
1.  **Agenda Items Covered**
2.  **Key Decisions**
3.  **Action Items** (Table: Action, Owner, Due Date)
4.  **Open Questions**

Produce the output in a file named `[output_file.md]`.
```

### Template 2: Technical Review Meeting

```
Analyze the technical review meeting transcript from `[source_file.txt]`.

**Meeting Title:** Technical Review: [Feature Name]
**Date:** [Specify Date]

**Extract the following information:**
1.  **Architecture Decisions:** Key choices made regarding the system design.
2.  **Code Review Feedback:** A summary of major feedback points.
3.  **Action Items:** Technical tasks assigned to engineers (Table: Task, Assignee, Target Sprint).
4.  **Identified Risks:** Any technical risks or blockers identified.

Save the summary to `[output_file.md]`.
```

### Template 3: Client-Facing Summary

```
Create a client-facing summary of our recent project update call. The transcript is in `[source_file.txt]`. The tone should be professional and concise.

**Meeting Title:** Project [Project Name] - Progress Update
**Date:** [Specify Date]

**Summary should include:**
1.  **Progress Since Last Meeting:** A brief overview of what we've accomplished.
2.  **Next Steps:** A clear list of the immediate next steps for both our team and the client's team.
3.  **Timeline Confirmation:** Reiterate the key upcoming milestones and dates.

Do not include internal-only discussions or technical jargon. Save the output to `[client_summary.md]`.
```

## References

- **Project Management Institute (PMI):** Best practices for meeting management and documentation.
- **Atlassian Confluence Documentation:** Examples of effective meeting notes templates.
- **Radical Candor by Kim Scott:** Principles for running effective and efficient meetings.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

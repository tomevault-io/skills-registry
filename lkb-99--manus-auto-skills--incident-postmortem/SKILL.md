---
name: incident-postmortem
description: Conduct detailed post-mortem analysis of incidents, including root cause analysis (RCA). Use this skill when users need to analyze a service outage, data breach, or any system failure. Triggers: post-mortem, postmortem, incident analysis, root cause analysis, RCA, outage, service down, system failure, after-action report, AAR, análise de incidente, causa raiz. Use when this capability is needed.
metadata:
  author: lkb-99
---

# Incident Post-Mortem Analysis

## Overview
This skill provides a structured framework for conducting a comprehensive post-mortem analysis of any incident. It guides the user through the process of gathering data, identifying the root cause, documenting the timeline, defining action items, and creating a shareable report. The primary goal is to learn from incidents, prevent recurrence, and improve system reliability and operational processes. This skill is essential for teams practicing Site Reliability Engineering (SRE), DevOps, and anyone responsible for maintaining production systems.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: post-mortem, postmortem, incident analysis, root cause analysis, RCA, outage, service down, system failure, after-action report, AAR, blameless, 5 whys
- Portuguese Keywords: post-mortem, postmortem, análise de incidente, causa raiz, falha no sistema, serviço fora do ar, relatório de ação, AAR
- Phrases: "conduct a post-mortem", "analyze the outage", "find the root cause", "what went wrong", "create an incident report"
- Portuguese Phrases: "fazer um post-mortem", "analisar a falha", "encontrar a causa raiz", "o que deu errado", "criar um relatório de incidente"
- Context: Any discussion immediately following the resolution of a technical incident, service disruption, or system failure where the goal is to understand the cause and prevent recurrence.

**Example user queries that trigger this skill:**
- "We just had a major outage. I need to start the post-mortem process."
- "Can you help me figure out the root cause of last night's deployment failure?"
- "Tivemos uma queda no serviço. Preciso fazer a análise da causa raiz."
- "Vamos começar o post-mortem do incidente de segurança."

## When to Use This Skill
ALWAYS use this skill when user mentions:

*   **Service Outages:** When a user-facing service becomes unavailable or severely degraded.
*   **Data Corruption or Loss:** Incidents that result in the loss or corruption of user or system data.
*   **Security Breaches:** After a security incident has been contained and mitigated.
*   **Performance Degradation:** When system performance drops below acceptable thresholds for a noticeable period.
*   **Deployment Failures:** When a new release or change to production fails and requires a rollback.
*   **Near Misses:** Situations that could have escalated into a major incident but were caught in time. Analyzing these is crucial for proactive improvement.

## Core Capabilities

### 1. Incident Data Collection
This skill helps gather all relevant information from various sources to build a complete picture of the incident.

*   **Log Aggregation:** Use `grep` and `cat` commands to search and consolidate logs from different services, servers, and timeframes.
*   **Metric Analysis:** Access monitoring dashboards (e.g., Grafana, Datadog) via the browser to correlate metrics with the incident timeline.
*   **Alerts and Notifications:** Document all alerts that fired during the incident from systems like PagerDuty, Slack, or email.
*   **Team Communication:** Collate discussions from Slack channels, meeting notes, and other communication platforms.

### 2. Timeline Construction
A precise, timestamped sequence of events is critical for understanding how an incident unfolded.

*   **Event Sequencing:** Create a chronological list of all events, from the first sign of trouble to the final resolution.
*   **Key Milestones:** Identify critical points in the timeline, such as: T-0 (incident start), time-to-detection (TTD), time-to-mitigation (TTM), and time-to-resolution (TTR).
*   **Automated Timeline Generation:** Use shell scripts to parse timestamps from logs and other structured data to help build the timeline.

### 3. Root Cause Analysis (RCA)
The core of the post-mortem is to understand the fundamental cause, not just the symptoms.

*   **The 5 Whys:** A simple, iterative technique to drill down to the root cause by repeatedly asking "Why?".
*   **Fishbone Diagram (Ishikawa):** A structured way to brainstorm potential causes by categorizing them (e.g., People, Process, Technology, Environment).
*   **Fault Tree Analysis:** A top-down, deductive failure analysis where a system failure is traced back to its root causes.

### 4. Action Item Definition
Translate learnings into concrete, actionable tasks to prevent recurrence.

*   **SMART Goals:** Define action items that are Specific, Measurable, Achievable, Relevant, and Time-bound.
*   **Ownership and Tracking:** Assign each action item to a specific owner and track its completion in a project management tool (e.g., Jira, Trello).
*   **Categorization:** Classify action items into categories like "Immediate Fix," "Short-Term Improvement," and "Long-Term Architectural Change."

### 5. Report Generation
Create a clear, concise, and blameless post-mortem report to share with stakeholders.

*   **Markdown Template:** The skill provides a pre-formatted Markdown template for the report.
*   **Audience-Specific Summaries:** Write different summaries for different audiences (e.g., executive summary, technical summary).
*   **Sharing and Archiving:** Store the final report in a centralized, accessible location like a wiki (Confluence) or a shared drive.

## Step-by-Step Workflow

1.  **Initiate Post-Mortem:** As soon as an incident is resolved, create a new directory for the post-mortem analysis.

    ```bash
    mkdir post-mortem-YYYY-MM-DD-incident-name
    cd post-mortem-YYYY-MM-DD-incident-name
    ```

2.  **Create Report from Template:** Use the `file` tool to create the `post-mortem-report.md` file from the template provided in the Examples section.

3.  **Gather Incident Data:**
    *   Use `shell` to run commands to collect logs. Example:
        ```bash
        # Search for errors in a specific time window
        grep "ERROR" /var/log/app.log --after-context=10 > error_logs.txt
        ```
    *   Use `browser` to navigate to monitoring dashboards and save relevant screenshots or data exports.
    *   Collect links to relevant Slack conversations, tickets, and alerts.

4.  **Fill in the Report Sections:**
    *   **Summary & Impact:** Write a high-level overview of the incident and its impact on users and the business.
    *   **Timeline:** Meticulously fill in the timeline of events with precise timestamps (in UTC).
    *   **Root Cause Analysis:** Use the "5 Whys" or another method to determine the root cause. Document your reasoning.

5.  **Define Action Items:**
    *   Brainstorm with the team to identify corrective and preventive actions.
    *   For each action item, define the task, the owner, and a due date.
    *   Use a Markdown table to list the action items in the report.

6.  **Review and Finalize:**
    *   Hold a blameless post-mortem meeting with all involved parties to review the draft report.
    *   Incorporate feedback and finalize the report.
    *   Ensure the tone is constructive and focused on learning, not blaming.

7.  **Share and Archive:**
    *   Share the report with all relevant stakeholders.
    *   Archive the report and all collected artifacts in a knowledge base for future reference.

## Best Practices

*   **Blameless Culture:** The primary rule of a post-mortem is to be blameless. Focus on systemic failures, not individual errors. Human error is a symptom, not a cause.
*   **Timeliness:** Conduct the post-mortem as soon as possible after the incident, while memories are still fresh.
*   **Involve the Right People:** Include everyone who was involved in the incident response, as well as representatives from affected teams.
*   **Be Thorough:** Don't stop at the first, most obvious cause. Dig deep to find the true root cause.
*   **Focus on Learning:** The goal is to understand what happened and how to prevent it from happening again. Every incident is a learning opportunity.
*   **Track Action Items:** A post-mortem is only useful if it leads to improvement. Rigorously track action items to completion.

## Examples

### Example 1: Post-Mortem Report Template

Use this template to create your `post-mortem-report.md` file.

```markdown
# Post-Mortem Report: [Incident Name]

| | |
| --- | --- |
| **Date** | YYYY-MM-DD |
| **Authors** | [List of Authors] |
| **Status** | [Draft, In Review, Final] |
| **Incident Lead** | [Name] |
| **Impact** | [e.g., 15-minute outage for 50% of users] |

## 1. Summary

*(A brief, one-paragraph summary of the incident, its impact, and the resolution. This should be understandable by a non-technical audience.)*

## 2. Impact

*   **User Impact:** [Describe how users were affected]
*   **Business Impact:** [Describe the impact on business metrics, e.g., revenue loss, SLA violation]
*   **Data Impact:** [Describe any data loss or corruption]

## 3. Timeline of Events (UTC)

| Timestamp (UTC) | Event Description |
| --- | --- |
| `HH:MM` | First alert fires: `[Alert Name]` |
| `HH:MM` | [Engineer Name] acknowledges the alert and starts investigating. |
| `HH:MM` | **[KEY EVENT]** A bad deploy (commit `[hash]`) is identified as the likely cause. |
| `HH:MM` | Rollback procedure is initiated. |
| `HH:MM` | Service is confirmed to be stable and fully recovered. |
| `HH:MM` | Incident is declared resolved. |

## 4. Root Cause Analysis

*(This section details the investigation that led to the root cause. The "5 Whys" method is recommended.)*

*   **Why 1?** The service was returning 500 errors.
    *   *Evidence:* [Link to logs or metrics]
*   **Why 2?** Because a database query was timing out.
    *   *Evidence:* [Link to APM trace]
*   **Why 3?** Because a new, un-indexed query was added in the last deploy.
    *   *Evidence:* [Link to commit or pull request]
*   **Why 4?** Because the pre-deployment code review process did not catch the performance impact of the new query.
    *   *Evidence:* [Link to code review comments]
*   **Why 5?** Because we lack an automated performance testing stage in our CI/CD pipeline to catch expensive queries before they reach production.
    *   **Root Cause:** Inadequate pre-deployment performance testing.

## 5. Lessons Learned

*   **What went well?**
    *   The on-call engineer responded quickly.
    *   The rollback procedure was fast and effective.
*   **What could be improved?**
    *   Time to detection was slow.
    *   Our code review process needs to be more rigorous about performance.
*   **Where did we get lucky?**
    *   The incident occurred during a low-traffic period.

## 6. Action Items

| # | Action Item | Owner | Due Date |
|---| --- | --- | --- |
| 1 | Add a database index for the new query. | @owner-name | YYYY-MM-DD |
| 2 | Implement a staging environment that mirrors production data size for performance testing. | @owner-name | YYYY-MM-DD |
| 3 | Integrate automated query analysis into the CI/CD pipeline. | @owner-name | YYYY-MM-DD |
| 4 | Update code review checklist to include performance considerations. | @owner-name | YYYY-MM-DD |

```

### Example 2: Shell command for finding relevant commits

```bash
# Find commits that touched a specific file in the last 7 days
git log --since="7 days ago" --oneline --follow -- path/to/problematic/file.py
```

## References

*   [Google SRE Book - Postmortem Culture](https://sre.google/sre-book/postmortem-culture/)
*   [Atlassian - Blameless Postmortems](https://www.atlassian.com/incident-management/blameless-postmortems)
*   [PagerDuty - The Postmortem Handbook](https://www.pagerduty.com/resources/learn/the-postmortem-handbook/)
*   [The 5 Whys: A Simple and Effective Root Cause Analysis Technique](https://www.isixsigma.com/tools-templates/cause-effect/determine-root-cause-5-whys/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

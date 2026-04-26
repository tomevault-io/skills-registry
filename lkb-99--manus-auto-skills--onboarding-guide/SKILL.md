---
name: onboarding-guide
description: Use when working with a comprehensive skill to create, manage, and automate onboarding guides. Use this skill when users want to create onboarding plans, guides for new employees, or integrate new members. Triggers: onboarding, new hire, new employee, integration, 30-60-90 plan, checklist, guia de integração, novo funcionário.
metadata:
  author: lkb-99
---

# Onboarding Guide

## Overview
The Onboarding Guide skill is designed to streamline the process of integrating new members into a team or organization. It helps create structured, comprehensive, and accessible onboarding documents that ensure a smooth and efficient ramp-up period. By leveraging this skill, you can automate the generation of personalized onboarding plans, track progress, and provide new hires with all the necessary information and resources in one centralized place. This skill is ideal for managers, HR professionals, and team leads who want to improve the onboarding experience and reduce the administrative overhead associated with it.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: onboarding, new hire, new employee, integration, 30-60-90 plan, checklist, guia de integração, novo funcionário, integração de equipe
- Phrases: "create an onboarding plan", "guide for new employees", "onboard a new team member", "criar um guia de integração", "plano de 30-60-90 dias"
- Context: Any discussion about integrating new members into a team or organization.

**Example user queries that trigger this skill:**
- "I need to create an onboarding guide for a new software engineer."
- "How can I help a new hire feel welcome?"
- "Preciso de um plano de integração para um novo membro da equipe."

## When to Use This Skill
This skill is particularly useful in the following scenarios:

- **Hiring New Employees:** When a new full-time or part-time employee joins the company.
- **Onboarding Freelancers or Contractors:** To quickly bring external collaborators up to speed on projects, tools, and company culture.
- **Internal Team Transfers:** When an existing employee moves to a new team or department and needs to learn new processes and responsibilities.
- **Internship Programs:** To provide a structured learning path for interns over a fixed period.
- **Scaling Teams:** When a company is growing rapidly and needs a standardized and scalable onboarding process.
- **Remote and Hybrid Teams:** To ensure remote employees receive the same quality of onboarding as their in-office counterparts.

## Core Capabilities

### 1. Onboarding Plan Generation
This capability allows you to generate a complete onboarding plan from a simple prompt or a predefined template. The plan can be customized based on the new member's role, team, and seniority level.

- **Role-Based Templates:** Generate plans tailored for specific roles like Software Engineer, Product Manager, Marketing Specialist, or Sales Representative.
- **30-60-90 Day Structure:** Automatically create a structured plan for the first three months, outlining key milestones, goals, and learning objectives.
- **Checklist Creation:** Generate detailed checklists for pre-boarding, first day, first week, and first month to ensure no critical step is missed.

### 2. Information and Resource Aggregation
Consolidate all essential information and resources into a single, easy-to-navigate guide. This prevents information overload and helps new hires find what they need quickly.

- **Company Information:** Include sections on company culture, values, mission, history, and organizational structure.
- **Tool and System Access:** Create a list of all necessary tools, software, and systems with instructions on how to get access.
- **Key Contacts:** Generate a list of important contacts, including the new hire's manager, mentor, HR business partner, and key team members.
- **Documentation Links:** Automatically find and embed links to important documents like the employee handbook, coding standards, project wikis, and process guides.

### 3. Task and Goal Management
Define and assign tasks and goals for the new team member to complete during their onboarding period. This helps them understand their responsibilities and what is expected of them.

- **Initial Project Assignment:** Outline the first project or set of tasks for the new hire to work on.
- **Learning Objectives:** Define specific learning goals, such as completing a training course, reading a book, or mastering a new tool.
- **Performance Goals:** Set clear performance expectations and KPIs for the first 30, 60, and 90 days.

### 4. Progress Tracking and Reporting
Monitor the new hire's progress throughout the onboarding process and generate reports for managers and HR.

- **Checklist Tracking:** Allow new hires and managers to check off completed items on the onboarding checklist.
- **Automated Reminders:** Send automated reminders for pending tasks and upcoming check-ins.
- **Feedback Collection:** Integrate forms or links to collect feedback from the new hire about their onboarding experience.

## Step-by-Step Workflow

1.  **Initiate the Skill:** Start by invoking the `onboarding-guide` skill and specifying the new team member's role and name.
    
    *Example Command:*
    ```
    manus onboarding-guide --role "Software Engineer" --name "Jane Doe"
    ```

2.  **Select a Template:** Choose from a list of predefined templates or opt to create a custom guide.
    
    *Options:*
    - `[1] General Employee Onboarding`
    - `[2] Software Engineer Onboarding`
    - `[3] Sales Development Representative Onboarding`
    - `[4] Create Custom Onboarding Guide`

3.  **Customize the Content:** The skill will generate a draft of the onboarding guide in a Markdown file. Open the file and customize the content as needed. You can add, remove, or edit sections to fit your specific requirements.
    
    *Action:*
    ```bash
    # The skill will create a file like onboarding-jane-doe.md
    # Open this file in your favorite editor
    code onboarding-jane-doe.md
    ```

4.  **Fill in Role-Specific Details:** The template will contain placeholders like `[Manager Name]`, `[Mentor Name]`, `[Project Name]`, etc. Use the `Edit` tool or your editor to replace these placeholders with the correct information.

5.  **Generate Checklists:** Use the skill to generate detailed checklists for different phases of the onboarding.
    
    *Example Command (within the skill's context):*
    ```
    generate checklist --type pre-boarding
    generate checklist --type first-week
    ```

6.  **Aggregate Resources:** Instruct the skill to find and link to relevant internal documentation. It can search your company's wiki, Google Drive, or other document repositories.
    
    *Example Command:*
    ```
    find docs --query "coding standards for python"
    find docs --query "company holiday schedule 2026"
    ```

7.  **Share the Guide:** Once the guide is complete, share the Markdown file or a rendered version (e.g., PDF, web page) with the new team member.

## Best Practices

- **Start Before Day One:** A great onboarding experience begins before the new hire's first day. Use the `pre-boarding` checklist to ensure their accounts are created, hardware is shipped, and initial paperwork is completed.
- **Assign a Buddy/Mentor:** A dedicated buddy or mentor is invaluable. The onboarding guide should clearly identify who this person is and what their role is in the onboarding process.
- **Personalize the Experience:** While templates are a great starting point, always personalize the guide for the individual. Include a personal welcome message from the manager and team.
- **Make it Interactive:** Include small tasks and questions in the guide to keep the new hire engaged. For example, "Find the #random channel in Slack and introduce yourself with a fun fact."
- **Schedule Regular Check-ins:** The guide should include a schedule of check-in meetings with the manager, mentor, and HR. This ensures the new hire has regular opportunities to ask questions and provide feedback.
- **Keep it Updated:** An onboarding guide is a living document. Regularly review and update it to reflect changes in processes, tools, and team structure.

## Examples

### Example 1: Creating an Onboarding Guide for a Software Engineer

**Command:**
```
manus onboarding-guide --role "Software Engineer" --name "Alex Ray"
```

**Generated `onboarding-alex-ray.md` (Excerpt):**
```markdown
# Welcome to the Team, Alex Ray!

## Your First 30 Days as a Software Engineer

### Week 1: Setup and Introduction

- [ ] **Day 1:**
  - [ ] Get your laptop and set up your development environment.
  - [ ] Attend the company-wide new hire orientation.
  - [ ] Have your first 1:1 with your manager, [Manager Name].
  - [ ] Meet your onboarding buddy, [Buddy Name].
- [ ] **By End of Week 1:**
  - [ ] You have access to all necessary systems: GitHub, Jira, Slack, etc.
  - [ ] You have cloned the main repository and have the project running locally.
  - [ ] You have completed your first small bug fix or feature ticket: [Jira-Ticket-Link].

### Key Resources:

- **Dev Environment Setup Guide:** [Link to Wiki]
- **Team Coding Standards:** [Link to Docs]
- **Our Git Workflow:** [Link to Guide]
```

### Example 2: Generating a Pre-boarding Checklist

**Command:**
```
# Inside the onboarding-guide skill context
generate checklist --type pre-boarding --for "Jane Doe"
```

**Generated Checklist:**
```markdown
### Pre-boarding Checklist for Jane Doe (To be completed by Manager)

- **1 Week Before Start Date:**
  - [ ] Send a welcome email to Jane, including her start time and first-day schedule.
  - [ ] Announce the new hire to the team.
  - [ ] Assign an onboarding buddy.
- **3 Days Before Start Date:**
  - [ ] **IT:** Ensure laptop and peripherals are configured and shipped.
  - [ ] **HR:** Confirm all employment paperwork is signed and returned.
  - [ ] **Access Management:** Create accounts for email, Slack, GitHub, Jira, etc.
```

### Example 3: Ready-to-Use Template for a 30-60-90 Day Plan

This template can be inserted into any onboarding guide.

```markdown
## Your 30-60-90 Day Plan

### Phase 1: First 30 Days - Learning and Integration

*The goal of this phase is to learn about the company, our products, our tools, and our processes. You will focus on getting your environment set up and completing your first small project.* 

- **Goals:**
  - Understand the company's mission, vision, and values.
  - Build relationships with your immediate team members.
  - Gain a high-level understanding of our product architecture.
  - Complete your first project/ticket.
- **Key Actions:**
  - Schedule 1:1 meetings with everyone on your team.
  - Read through the company handbook and key technical documentation.
  - Pair program with a senior engineer for at least 4 hours.

### Phase 2: First 60 Days - Contribution and Collaboration

*The goal of this phase is to start contributing more independently to the team's projects and to collaborate more broadly with other teams.* 

- **Goals:**
  - Take ownership of a medium-sized feature or component.
  - Contribute to team discussions and planning meetings.
  - Identify an area for improvement in our processes or codebase.
- **Key Actions:**
  - Lead the technical breakdown of a new feature.
  - Participate in a code review for another team's project.
  - Present your improvement idea to the team.

### Phase 3: First 90 Days - Ownership and Impact

*By the end of this phase, you should be a fully integrated and productive member of the team, taking ownership of your work and making a tangible impact.*

- **Goals:**
  - Independently manage and deliver a complex project from start to finish.
  - Act as a resource for newer team members.
  - Demonstrate a deep understanding of your domain.
- **Key Actions:**
  - Mentor a new intern or junior engineer.
  - Write a technical design document for a major new feature.
  - Ship a feature that moves a key company metric.
```

## References

- [The First 90 Days: Proven Strategies for Getting Up to Speed Faster and Smarter, by Michael D. Watkins](https://www.amazon.com/First-90-Days-Strategies-Smarter/dp/1422188612)
- [Google's Re-Work: A manager’s guide to onboarding](https://rework.withgoogle.com/guides/managers-guide-to-onboarding/)
- [GitLab's Onboarding Guide](https://about.gitlab.com/handbook/general-onboarding/)
- [A Guide to Successful Employee Onboarding - SHRM](https://www.shrm.org/resourcesandtools/hr-topics/talent-acquisition/pages/new-employee-onboarding-guide.aspx)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

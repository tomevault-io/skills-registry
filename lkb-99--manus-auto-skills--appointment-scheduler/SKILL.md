---
name: appointment-scheduler
description: "Use this skill to schedule, manage, and automate appointments, meetings, and events. Triggers: schedule, appointment, meeting, book, calendar, event, reminder, sync, block time, agendar, consulta, reunião, marcar, calendário, evento, lembrete."
allowed-tools: [Read, Write, Edit, Bash, Browser]
license: MIT License
metadata:
    skill-author: Lucas Kefler Bergamaschi
---

# Appointment Scheduler

## Overview

The **Appointment Scheduler** skill empowers Manus to manage scheduling-related tasks efficiently. It can create, modify, cancel, and list appointments, meetings, and other events. The core functionality revolves around interacting with calendar services and communication tools to check availability, book slots, and send automated reminders to participants. This skill is designed to be a comprehensive solution for personal and professional scheduling, reducing the manual effort involved in coordinating with multiple people.

This skill leverages integrations with external calendar providers like Google Calendar and Outlook Calendar through MCP (Model Context Protocol) tools. By doing so, it can access real-time availability, prevent double-booking, and ensure that all events are synchronized across devices and platforms. The ultimate goal is to provide a seamless and intelligent scheduling assistant that understands natural language requests and executes them reliably.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: schedule, appointment, meeting, book, calendar, event, reminder, sync, block time, find time, availability, agendar, agendamento, consulta, reunião, marcar, calendário, evento, lembrete, bloquear horário, encontrar horário, disponibilidade.
- Phrases: "schedule a meeting", "book an appointment", "find a time to talk", "set a reminder", "check my calendar", "marcar uma reunião", "agendar uma consulta", "verificar minha agenda", "criar um evento".
- Context: Any discussion about managing time, coordinating with others, or setting up events in a calendar.

**Example user queries that trigger this skill:**
- "Can you schedule a meeting with John for tomorrow at 10am?"
- "Preciso marcar uma consulta com o Dr. Silva na próxima semana."
- "Find a 30-minute slot for me and Maria next Monday."
- "Set a reminder for my dentist appointment."

## When to Use This Skill

ALWAYS use this skill when the user needs to:

*   **Schedule professional meetings:** Book meetings with clients, colleagues, or business partners. This includes finding mutually available times, sending invitations, and booking meeting rooms.
*   **Manage client appointments:** For professionals like consultants, lawyers, or freelancers, this skill can handle booking, rescheduling, and reminding clients about their appointments.
*   **Coordinate team events:** Organize team stand-ups, brainstorming sessions, or project check-ins. The skill can handle recurring events and notify all team members.
*   **Book personal appointments:** Schedule personal events like doctor's appointments, dinner reservations, or calls with friends and family.
*   **Manage interview processes:** For recruiters or hiring managers, this skill can streamline the process of scheduling interviews with candidates, sending confirmations, and follow-up reminders.
*   **Automate reminders:** Set up reminders for any important event, ensuring you and other participants do not miss it.
*   **Check calendar availability:** Quickly find out when you or your colleagues are free without having to manually check multiple calendars.

## Core Capabilities

This skill is built upon a set of core capabilities that enable comprehensive scheduling management.

### 1. Create and Book Appointments
This is the primary function of the skill. Manus can parse natural language requests to create new events. The creation process includes identifying key details such as the event title, participants, date, time, duration, and location (physical or virtual).

**Key Features:**
- **Natural Language Understanding:** Parses requests like "Book a meeting with John Doe for tomorrow at 2 PM to discuss the project proposal."
- **Participant Management:** Adds attendees to the event and sends them invitations.
- **Time Zone Awareness:** Automatically handles time zone conversions to avoid confusion when scheduling with people in different locations.
- **Resource Booking:** Can book shared resources like meeting rooms or equipment if integrated with the organization's resource management system.

```bash
# Example of an internal command to create an event
manus-mcp-cli tool call create-event --server google-calendar --input '{
  "summary": "Project Kick-off Meeting",
  "description": "Initial meeting to discuss the scope and timeline of the new project.",
  "start_time": "2026-03-15T10:00:00-05:00",
  "end_time": "2026-03-15T11:00:00-05:00",
  "attendees": ["user@example.com", "colleague@example.com"],
  "location": "Virtual / Google Meet"
}'
```

### 2. Read and List Appointments
Manus can retrieve and display a list of upcoming or past appointments. This allows users to quickly get an overview of their schedule.

**Key Features:**
- **Flexible Filtering:** Users can ask for appointments on a specific day, week, or month, or search for events with a specific person or keyword.
- **Formatted Output:** Presents the schedule in a clear, easy-to-read format, such as a list or a table.

```bash
# Example of a request to list appointments
"Show me my schedule for tomorrow."

# Example of an internal command to list events
manus-mcp-cli tool call list-events --server google-calendar --input '{
  "start_time": "2026-03-10T00:00:00Z",
  "end_time": "2026-03-10T23:59:59Z"
}'
```

### 3. Update and Reschedule Appointments
Mistakes happen, and plans change. This capability allows users to modify existing appointments, whether it's changing the time, adding a participant, or updating the description.

**Key Features:**
- **Targeted Identification:** Manus can identify the specific event the user wants to change based on their request.
- **Conflict Checking:** When rescheduling, the skill can automatically check for conflicts with other events on the participants' calendars.

```bash
# Example of a request to reschedule
"Reschedule my 10 AM meeting with Jane to 3 PM."

# Example of an internal command to update an event
manus-mcp-cli tool call update-event --server google-calendar --input '{
  "event_id": "abcdef123456",
  "start_time": "2026-03-15T15:00:00-05:00",
  "end_time": "2026-03-15T16:00:00-05:00"
}'
```

### 4. Cancel and Delete Appointments
Users can easily cancel appointments that are no longer needed. The skill will remove the event from the calendar and notify all participants about the cancellation.

**Key Features:**
- **Confirmation Step:** By default, Manus will ask for confirmation before deleting an event to prevent accidental cancellations.
- **Automated Notifications:** Sends cancellation notices to all attendees.

```bash
# Example of a request to cancel
"Cancel my project sync meeting this afternoon."

# Example of an internal command to delete an event
manus-mcp-cli tool call delete-event --server google-calendar --input '{
  "event_id": "ghijkl789012"
}'
```

### 5. Automated Reminders
To help users stay on top of their schedule, the skill can be configured to send automated reminders before an event starts. Reminders can be sent via email, SMS, or other notification channels integrated with Manus.

**Key Features:**
- **Customizable Timing:** Users can specify when they want to receive reminders (e.g., 1 hour before, 24 hours before).
- **Multiple Channels:** Can integrate with tools like `gmail` or `outlook-mail` to send email reminders.

### 6. Check Availability
Before scheduling a new meeting, it's crucial to know when the intended participants are free. This skill can query the calendars of specified individuals to find common free slots.

**Key Features:**
- **Group Availability:** Finds time slots that work for a group of people.
- **Working Hours:** Can respect the working hours defined in the participants' calendar settings.

```bash
# Example of a request to check availability
"Find a 30-minute slot for me and Bob next week."

# Example of an internal command to check free/busy status
manus-mcp-cli tool call get-free-busy --server google-calendar --input '{
  "start_time": "2026-03-16T09:00:00-05:00",
  "end_time": "2026-03-20T17:00:00-05:00",
  "calendars": ["user@example.com", "bob@example.com"]
}'
```

## Step-by-Step Workflow

Here is a typical workflow for using the Appointment Scheduler skill:

1.  **Initiate the Request:** The user makes a request to schedule an appointment. For example: `"Hey Manus, can you schedule a 1-hour meeting with Sarah next Tuesday to review the quarterly report?"`

2.  **Gather Information:** Manus analyzes the request. If any information is missing (e.g., Sarah's email or preferred time of day), it will ask clarifying questions: `"Certainly. What is Sarah's email address? And are there any specific times on Tuesday that work best?"`

3.  **Check Availability:** Once all details are gathered, Manus uses the `google-calendar` or `outlook-calendar` MCP tool to check the availability of all participants for next Tuesday.

4.  **Propose Times:** Based on the availability check, Manus proposes a few suitable time slots to the user: `"Sarah and you are both free at 11 AM, 2 PM, and 4 PM next Tuesday. Which time would you like to book?"`

5.  **User Confirmation:** The user confirms their preferred time: `"Let's go with 2 PM."`

6.  **Create the Event:** Manus uses the appropriate MCP tool to create the event on the calendar, including all details like title, attendees, time, and a link for a virtual meeting if applicable.

7.  **Final Confirmation:** Manus confirms the successful booking with the user: `"Great. I have scheduled the meeting 'Quarterly Report Review' with Sarah for next Tuesday at 2 PM. An invitation has been sent to her. I will also send you both a reminder one hour before the meeting."`

8.  **Send Reminder:** At the configured time (e.g., one hour before the meeting), an automated process is triggered, and Manus uses a tool like `gmail` to send a reminder email to both the user and Sarah.

## Templates for Scheduling

Using templates can significantly speed up the process of scheduling common types of appointments. You can create and manage these templates as simple text files or within a more structured knowledge base that Manus can access.

### Template 1: New Client Onboarding Call

- **Name:** `new-client-onboarding`
- **Duration:** 60 minutes
- **Title:** "Onboarding Call: [Client Name] & [Your Name]"
- **Description:**

    "Welcome aboard!

    This call is to kick off our partnership, discuss your goals, and outline the next steps.

    Agenda:
    1.  Introductions
    2.  Review of your objectives
    3.  Overview of our process
    4.  Q&A

    Looking forward to speaking with you!"

- **Default Reminder:** 24 hours before

### Template 2: Weekly Team Sync

- **Name:** `weekly-team-sync`
- **Duration:** 30 minutes
- **Title:** "Weekly Team Sync"
- **Description:** "Quick sync to cover weekly progress, blockers, and priorities."
- **Attendees:** `team@example.com`
- **Recurrence:** Every Monday at 10:00 AM
- **Default Reminder:** 15 minutes before

### Template 3: Job Interview

- **Name:** `job-interview`
- **Duration:** 45 minutes
- **Title:** "Interview: [Candidate Name] for [Position Title]"
- **Description:**

    "Hello [Candidate Name],

    Thank you for your interest in the [Position Title] role at [Company Name].

    This is an invitation for a 45-minute interview with [Interviewer Name] to discuss your background and the position in more detail.

    Please let us know if you have any questions beforehand.

    Best regards,
    [Your Name]"

- **Default Reminder:** 1 day before

### How to Use a Template

**User Prompt:** `"Use the 'new-client-onboarding' template to schedule a call with 'Innovate Corp' for next week."`

**Manus Actions:**
1.  Loads the `new-client-onboarding` template.
2.  Asks for the contact person's email at Innovate Corp if not already known.
3.  Finds a 60-minute slot that works for both parties next week.
4.  Creates the event using the template's title and description, filling in the client's name.
5.  Sets a reminder for 24 hours before the meeting.

## Best Practices

To get the most out of the Appointment Scheduler skill, follow these best practices:

*   **Be Specific:** Provide as much detail as possible in your initial request. Include the full names of participants, their email addresses if known, the desired duration, and a clear topic for the meeting. This reduces the need for back-and-forth questions.
*   **Define Time Zones:** When scheduling with people in different geographic locations, always specify the time zone to avoid confusion. For example: `"Book a call with Alex (alex@example.com) for 9 AM PST."`
*   **Use Clear Titles:** A descriptive title helps participants understand the purpose of the meeting at a glance. Instead of "Meeting," use "Marketing Sync - Q3 Campaign Planning."
*   **Leverage Templates:** For recurring or common appointment types, use templates to save time and ensure consistency.
*   **Confirm Before Cancelling:** Always double-check before asking Manus to cancel an appointment, especially if it involves other people.

By following these guidelines, you can turn Manus into a powerful and reliable scheduling assistant.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

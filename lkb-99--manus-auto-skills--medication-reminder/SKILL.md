---
name: medication-reminder
description: Manage medication schedules, get reminders, and check for drug interactions. Use this skill when users want to track medications, set reminders for pills, or check for drug safety. Triggers: medication reminder, lembrete de remédio, pill reminder, medicine schedule, horário de medicação, drug interaction, interação medicamentosa, manage my meds. Use when this capability is needed.
metadata:
  author: lkb-99
---

# Medication Reminder & Drug Interaction Skill

## Overview
This skill provides a robust system for managing medication schedules, sending timely reminders, and automatically checking for potential drug interactions. It is designed to help users, caregivers, and healthcare professionals ensure medication adherence and safety. By leveraging external APIs and internal data storage, the skill can maintain a personalized medication list, track dosages, and alert users to potential conflicts between their prescribed drugs. This is particularly useful for individuals with complex medication regimens, chronic conditions, or those under the care of multiple specialists.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: medication reminder, lembrete de remédio, pill reminder, medicine schedule, horário de medicação, drug interaction, interação medicamentosa, manage my meds, tomar remédio, controle de medicação
- Phrases: "set a reminder for my medication", "preciso de um alarme para meu remédio", "check if my drugs interact", "verificar interação de medicamentos", "gerenciar meus remédios"
- Context: Any discussion about managing, scheduling, remembering, or checking safety of medications.

**Example user queries that trigger this skill:**
- "Você pode me lembrar de tomar meu remédio às 8h?"
- "I need to add a new medication to my list."
- "Verifique se a amoxicilina interage com a varfarina."
- "Como posso controlar os horários dos meus medicamentos?"

## When to Use This Skill
This skill is most effective in the following scenarios:

*   **Complex Medication Schedules:** When a user needs to manage multiple medications with different dosages, frequencies, and times.
*   **Elderly Care:** For caregivers or family members managing the medications of an elderly person who may have difficulty remembering their schedule.
*   **Chronic Illness Management:** For patients with chronic conditions like hypertension, diabetes, or heart disease who require long-term medication adherence.
*   **Post-Hospitalization:** To help patients transition from hospital to home by ensuring they follow their new and often complex medication plans.
*   **Polypharmacy:** When a user is taking five or more medications, increasing the risk of drug interactions.
*   **Improving Adherence:** For any individual who wants to improve their consistency in taking medications as prescribed.
*   **Safety Checks:** When adding a new medication (prescription or over-the-counter) and needing to check for interactions with existing medications.

## Core Capabilities

### 1. Medication Management
The skill allows users to add, edit, and remove medications from their personal list. Each entry can include:
*   **Drug Name:** (e.g., Lisinopril)
*   **Dosage:** (e.g., 10 mg)
*   **Form:** (e.g., Tablet, Capsule, Liquid)
*   **Frequency:** (e.g., Once daily, Twice daily, Every 8 hours)
*   **Specific Times:** (e.g., 8:00 AM, 9:00 PM)
*   **Instructions:** (e.g., Take with food, Take on an empty stomach)
*   **Prescribing Doctor:** (Optional, for tracking)
*   **Start and End Dates:** (For short-term medications like antibiotics)

**Example JSON structure for a medication:**
```json
{
  "medication_id": "med_12345",
  "drug_name": "Lisinopril",
  "dosage": "10 mg",
  "form": "Tablet",
  "schedule": {
    "frequency": "Once daily",
    "times": ["08:00"]
  },
  "instructions": "Take with a full glass of water.",
  "prescriber": "Dr. Smith",
  "start_date": "2026-01-15",
  "end_date": null
}
```

### 2. Reminder System
The core of the skill is a persistent reminder system. It uses the system's scheduling capabilities (like cron jobs or a background scheduler) to trigger notifications.
*   **Customizable Alerts:** Users can choose their preferred notification method (e.g., system notification, email, SMS - future integration).
*   **Snooze Functionality:** Allows users to delay a reminder for a short period (e.g., 10, 15, or 30 minutes).
*   **Confirmation Logging:** When a user confirms they have taken the medication, the skill logs the time and date, creating an adherence history.

**Example command to set a reminder:**
```bash
# This is a conceptual command that the skill's logic would execute
# It would add a job to a scheduler (e.g., cron)
(crontab -l ; echo "0 8 * * * /usr/bin/notify-send 'Medication Reminder' 'Time to take your Lisinopril (10 mg)'") | crontab -
```

### 3. Drug Interaction Checking
This is a critical safety feature. Before adding a new medication, the skill automatically cross-references it with the user's existing medication list.
*   **API Integration:** It uses a reliable drug interaction API (like the NIH's RxNorm or OpenFDA) to check for potential interactions.
*   **Severity Levels:** Interactions are categorized by severity (e.g., High, Moderate, Low) to help the user and their doctor make informed decisions.
*   **Clear Explanations:** The skill provides a plain-language explanation of the potential interaction and advises the user to consult their healthcare provider.

**Example API call (conceptual):**
```python
import requests

def check_interactions(new_drug, existing_drugs):
    api_url = "https://api.fda.gov/drug/label.json"
    # In a real implementation, you would use a dedicated drug interaction endpoint
    # This is a simplified example using OpenFDA
    drug_list = [new_drug] + existing_drugs
    query = '"' + '" "'.join(drug_list) + '"'
    params = {
        'search': f'"drug_interactions":({query})',
        'limit': 5
    }
    response = requests.get(api_url, params=params)
    if response.status_code == 200:
        return response.json()
    return None
```

### 4. Adherence Tracking and Reporting
The skill maintains a log of when medications are taken, missed, or snoozed.
*   **History View:** Users can view their adherence history over a week, month, or custom date range.
*   **Adherence Score:** Calculates a percentage score for adherence to help users track their progress.
*   **Exportable Reports:** The skill can generate a simple text or CSV report that can be shared with a doctor or caregiver.

**Example adherence log entry:**
```
2026-02-01 08:05:00,Lisinopril,10 mg,TAKEN
2026-02-01 20:00:00,Atorvastatin,20 mg,MISSED
2026-02-02 08:00:00,Lisinopril,10 mg,SNOOZED
2026-02-02 08:15:00,Lisinopril,10 mg,TAKEN
```

## Step-by-Step Workflow

1.  **Initialization (`init`):**
    *   The user runs the skill for the first time.
    *   The skill creates a hidden directory (e.g., `~/.medication_reminder`) to store user data.
    *   It creates two primary files: `medications.json` (for the list of medications) and `adherence.log`.
    *   It asks the user for basic setup information, such as preferred notification times (morning, noon, evening).

2.  **Add a Medication (`add`):**
    *   The user invokes the `add` command.
    *   The skill prompts for the drug name, dosage, frequency, and other details.
    *   **Crucially, it calls the drug interaction API** to check the new drug against all existing drugs in `medications.json`.
    *   If a high-severity interaction is found, it warns the user and requires confirmation before adding.
    *   If confirmed, the new medication is appended to `medications.json`.
    *   The skill updates the system's scheduler to add the new reminders.

3.  **List Medications (`list`):**
    *   The user invokes the `list` command.
    *   The skill reads `medications.json` and displays a formatted list of all current medications and their schedules.

4.  **Receive a Reminder:**
    *   At the scheduled time, the system scheduler triggers a notification.
    *   The notification includes the drug name and dosage.
    *   The notification provides options: `Take`, `Skip`, `Snooze`.

5.  **Log Adherence (`log`):**
    *   Based on the user's action on the reminder, the skill appends a new entry to `adherence.log`.
    *   This action is critical for tracking and reporting.

6.  **Check Adherence (`report`):**
    *   The user invokes the `report` command.
    *   The skill parses `adherence.log` for a specified period (default: last 7 days).
    *   It calculates and displays the adherence rate and a summary of taken/missed doses.

7.  **Remove a Medication (`remove`):**
    *   The user invokes the `remove` command with a medication ID or name.
    *   The skill removes the medication from `medications.json`.
    *   It also removes the corresponding jobs from the system scheduler to stop future reminders.

## Best Practices

*   **Data Privacy and Security:** All medication data is stored locally on the user's machine. The skill should never transmit this data to an external server without explicit user consent. When calling APIs, only transmit the necessary drug names, not personal user information.
*   **Disclaimer:** The skill must display a clear and prominent disclaimer during initialization and in its help section. It should state that it is not a substitute for professional medical advice and that all medication changes and concerns should be discussed with a qualified healthcare provider.
*   **Error Handling:** The skill should gracefully handle API failures (e.g., no internet connection) by informing the user that interaction checking is temporarily unavailable.
*   **User-Friendly Interface:** Use clear and simple language. Avoid medical jargon where possible. Prompts should be easy to understand and follow.
*   **Regular Backups:** Encourage users to back up the skill's data directory, as it contains their complete medication history.
*   **Consult a Professional:** Always reinforce that the drug interaction checker is a tool for awareness, not a diagnostic tool. The final decision should always be made by a doctor or pharmacist.

## Examples

### Example 1: Adding a First Medication

**User Action:** `manus medication-reminder --action add`

**Skill Prompts & User Input:**
```
> Enter drug name: Atorvastatin
> Enter dosage (e.g., 20 mg): 20 mg
> Enter frequency (e.g., Once daily): Once daily
> Enter time (e.g., 21:00): 21:00
> Any special instructions? (optional): Take in the evening
```

**Skill Output:**
```
[INFO] No existing medications found. Interaction check skipped.
[SUCCESS] Atorvastatin (20 mg) has been added to your schedule for 9:00 PM daily.
[INFO] A reminder has been set.
```

### Example 2: Adding a Second Medication with an Interaction

**User Action:** `manus medication-reminder --action add`

**Skill Prompts & User Input:**
```
> Enter drug name: Clarithromycin
> Enter dosage (e.g., 500 mg): 500 mg
> Enter frequency: Twice daily
> Enter times (comma-separated, e.g., 09:00,21:00): 09:00,21:00
```

**Skill Output (after API call):**
```
[WARNING] Potential Drug Interaction Found!

Severity: High

Interaction: Clarithromycin may increase the blood levels of Atorvastatin, raising the risk of serious side effects like rhabdomyolysis (muscle breakdown).

Disclaimer: This is not medical advice. Please consult your doctor or pharmacist before taking these medications together.

> Do you still want to add Clarithromycin to your schedule? (yes/no): yes
```

**Skill Output (if user confirms):**
```
[SUCCESS] Clarithromycin (500 mg) has been added to your schedule for 9:00 AM and 9:00 PM daily.
[INFO] Reminders have been set.
[IMPORTANT] Please discuss this potential interaction with your healthcare provider immediately.
```

### Example 3: Viewing the Daily Report

**User Action:** `manus medication-reminder --action report --period daily`

**Skill Output:**
```
Adherence Report for Today (2026-02-03)

- 09:00 AM | Clarithromycin (500 mg) | TAKEN at 09:02 AM
- 08:00 AM | Lisinopril (10 mg)      | MISSED
- 09:00 PM | Clarithromycin (500 mg) | PENDING
- 09:00 PM | Atorvastatin (20 mg)    | PENDING

Daily Adherence Rate: 50.0%
```

## Templates

### Template: `medications.json`

```json
[
  {
    "medication_id": "med_67890",
    "drug_name": "Atorvastatin",
    "dosage": "20 mg",
    "form": "Tablet",
    "schedule": {
      "frequency": "Once daily",
      "times": ["21:00"]
    },
    "instructions": "Take in the evening",
    "prescriber": "Dr. Jones",
    "start_date": "2025-11-20",
    "end_date": null
  },
  {
    "medication_id": "med_12345",
    "drug_name": "Lisinopril",
    "dosage": "10 mg",
    "form": "Tablet",
    "schedule": {
      "frequency": "Once daily",
      "times": ["08:00"]
    },
    "instructions": "Take with a full glass of water.",
    "prescriber": "Dr. Smith",
    "start_date": "2026-01-15",
    "end_date": null
  }
]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

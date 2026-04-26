---
name: telemedicine-assistant
description: Use when working with a comprehensive assistant for medical teleconsultations. Use this skill when users need to conduct, document, or prepare for a virtual medical appointment, teleconsultation, or remote patient visit. Triggers: telemedicine, teleconsultation, telemedicina, teleconsulta, consulta online, consulta virtual, teletriagem, remote consultation, virtual visit, e-health, digital health, remote patient monitoring.
metadata:
  author: lkb-99
---

# Telemedicine Assistant: Your Partner for Efficient and Safe Teleconsultations

## Overview

The Telemedicine Assistant skill is designed to support healthcare professionals, like Dr. Lucas Kefler Bergamaschi, in conducting effective, safe, and well-documented teleconsultations. In an era where digital health is paramount, this skill acts as a digital co-pilot, streamlining the entire telemedicine workflow from pre-consultation preparation to post-consultation follow-up. It provides structured checklists, standardized documentation templates, and communication aids to ensure that the quality of care in a virtual setting is equivalent to an in-person visit. This tool is particularly useful for physicians who manage complex cases involving regenerative medicine, pain management, and integrative therapies, where detailed patient history and meticulous documentation are critical.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: telemedicine, teleconsultation, telemedicina, teleconsulta, consulta online, consulta virtual, teletriagem, remote consultation, virtual visit, e-health, digital health, remote patient monitoring, atendimento online, consulta por vídeo.
- Phrases: "realizar uma teleconsulta", "preparar para uma consulta online", "documentar uma telemedicina", "assistente de telemedicina", "checklist para teleconsulta", "consentimento para telemedicina".
- Context: Any discussion about conducting virtual medical appointments, preparing documentation for remote consultations, or managing patient communication for telemedicine.

**Example user queries that trigger this skill:**
- "Preciso de um checklist para minha próxima teleconsulta."
- "Como eu documento uma consulta por vídeo no prontuário?"
- "Gere um modelo de consentimento informado para telemedicina."
- "Help me prepare for a remote patient consultation."

## When to Use This Skill

This skill is indispensable in a variety of telemedicine scenarios. Its application ensures consistency and thoroughness, which is vital for both patient safety and legal protection.

- **Initial Teleconsultation:** When meeting a patient for the first time virtually. The skill provides a comprehensive checklist to gather all necessary information, from medical history to the patient's technological setup.
- **Follow-up Appointments:** For monitoring patient progress, adjusting treatment plans, and providing ongoing care. The templates help in quickly documenting changes and outcomes.
- **Chronic Disease Management:** Assisting patients with long-term conditions that require regular check-ins. The structured workflow helps in maintaining continuity of care.
- **Pre-Procedure Counseling:** When explaining a minimally invasive procedure, such as a prolotherapy or PRP injection, to a patient remotely. The skill provides a framework for informed consent.
- **Post-Procedure Follow-up:** To check on a patient's recovery, manage any side effects, and document the outcomes of a procedure.
- **Issuing Prescriptions or Referrals:** When a teleconsultation results in the need for a prescription, a referral to another specialist, or a request for lab tests.
- **Multi-disciplinary Case Discussions:** When collaborating with other healthcare professionals, the standardized documentation ensures that all relevant information is shared clearly and concisely.

## Core Capabilities

This skill is built upon a foundation of several core capabilities designed to address the unique challenges of telemedicine.

### 1. Pre-Consultation Checklist

To ensure a smooth and effective consultation, a structured preparation process is essential. This capability generates a dynamic checklist for both the physician and the patient.

**Physician's Checklist:**
- [ ] Review patient's medical records, previous consultations, and lab results.
- [ ] Prepare a list of specific questions related to the patient's condition.
- [ ] Ensure a stable internet connection and a professional, private, and well-lit environment.
- [ ] Test audio and video equipment beforehand.
- [ ] Have all necessary digital tools and resources open (EHR, imaging viewer, etc.).
- [ ] Prepare a contingency plan for technical difficulties (e.g., a follow-up phone call).

**Patient's Pre-Consultation Instructions (Template):**
```
Subject: Instructions for Your Upcoming Teleconsultation with Dr. [Your Name]

Dear [Patient Name],

To ensure we have a productive and smooth teleconsultation, please follow these steps before our appointment on [Date] at [Time]:

1.  **Find a Quiet Space:** Choose a private, quiet, and well-lit area for our call.
2.  **Check Your Tech:** Please use a device with a stable internet connection, a camera, and a microphone. A smartphone, tablet, or computer is suitable. Test your equipment beforehand.
3.  **List Your Concerns:** Write down your symptoms, questions, and any medications or supplements you are currently taking.
4.  **Have Information Ready:** If possible, have your medical history, recent test results, and a list of your current medications available.
5.  **Join the Call Early:** Please click the consultation link 5 minutes before our scheduled time to resolve any technical issues.

We look forward to speaking with you.

Sincerely,
Dr. [Your Name]
```

### 2. Structured Clinical Documentation

Proper documentation is the cornerstone of good medical practice. This capability provides detailed templates for structuring clinical notes during a teleconsultation, ensuring all critical information is captured.

**SOAP Note Template for Telemedicine:**

```markdown
**Patient:** [Patient Name]
**Date of Consultation:** [Date]
**Chief Complaint:** [Patient's primary reason for the visit]

**S (Subjective):**
- **History of Present Illness (HPI):** [Detailed account of the patient's symptoms, including onset, location, duration, character, alleviating/aggravating factors, radiation, and timing.]
- **Review of Systems (ROS):** [Systematic review of other body systems.]
- **Past Medical History:** [Chronic illnesses, past surgeries, hospitalizations.]
- **Medications & Allergies:** [List of current medications, supplements, and known allergies.]
- **Social History:** [Lifestyle factors, occupation, habits (smoking, alcohol).]

**O (Objective):**
- **Virtual Physical Examination:** [Describe observations made via video. E.g., range of motion, skin inspection, neurological assessment (if applicable). Note limitations of the virtual exam.]
- **Review of Uploaded Data:** [Mention any patient-provided photos, videos, or data from medical devices.]
- **Review of Lab/Imaging Results:** [Summarize findings from any available reports.]

**A (Assessment):**
- **Primary Diagnosis:** [Your primary diagnosis based on the subjective and objective findings.]
- **Differential Diagnoses:** [List other possible diagnoses.]
- **Problem List:** [A numbered list of the patient's active medical problems.]

**P (Plan):**
- **Diagnostic Plan:** [Further tests or imaging ordered.]
- **Therapeutic Plan:** [Medications prescribed, procedures recommended, lifestyle changes advised.]
- **Patient Education:** [Information provided to the patient about their condition and treatment.]
- **Follow-up:** [When and how the next follow-up will occur (e.g., teleconsultation in 4 weeks).]
- **Contingency Plan:** [Instructions for the patient in case of worsening symptoms or emergencies.]
```

### 3. Informed Consent for Telemedicine

Ensuring the patient understands the nature, risks, and benefits of a teleconsultation is a legal and ethical requirement. This capability generates a template for obtaining and documenting informed consent.

**Telemedicine Informed Consent Template:**

```
**Verbal Consent Obtained and Documented**

During the teleconsultation on [Date], I, Dr. [Your Name], explained the following to the patient, [Patient Name]:

1.  **Nature of Telemedicine:** I explained that a teleconsultation is a medical appointment conducted via video and audio, and it has inherent limitations compared to an in-person examination.
2.  **Benefits:** The benefits, including convenience and access to care, were discussed.
3.  **Risks & Limitations:** The potential risks were outlined, including:
    *   The inability to perform a complete physical examination.
    *   The possibility of technical failures or interruptions.
    *   Privacy and security risks associated with electronic communication.
4.  **Patient's Rights:** I informed the patient of their right to withdraw consent at any time and to opt for an in-person consultation if they prefer.
5.  **Data Privacy:** I explained how their personal health information would be protected and stored securely.

The patient verbally confirmed their understanding and agreed to proceed with the teleconsultation. They had the opportunity to ask questions, and all their questions were answered to their satisfaction.
```

## Step-by-Step Workflow

Follow these steps to integrate the Telemedicine Assistant into your practice.

1.  **Initiate the Skill:** Before a scheduled teleconsultation, invoke the `telemedicine-assistant` skill.
2.  **Generate Pre-Consultation Checklist:** Use the `generate-checklist` command. This will produce the physician's checklist and a template email to send to the patient.
3.  **Prepare for the Consultation:** Follow your checklist. Review patient files, test your equipment, and send the instructional email to the patient.
4.  **Start the Consultation:** At the scheduled time, begin the video call with the patient.
5.  **Obtain Informed Consent:** At the beginning of the call, use the `generate-consent-template` command. Read the key points to the patient and document their verbal consent in their medical record.
6.  **Conduct the Consultation & Document:** Use the `generate-soap-note` command to create a structured template for your clinical notes. Fill it out in real-time as you speak with the patient.
7.  **Formulate the Plan:** Based on your assessment, complete the 'Plan' section of the SOAP note. Discuss the plan with the patient, ensuring they understand the next steps.
8.  **Conclude the Consultation:** Summarize the key points of the consultation, confirm the follow-up plan, and answer any final questions.
9.  **Finalize Documentation:** After the call, review and finalize your clinical notes. Ensure all sections are complete and accurate.
10. **Execute the Plan:** Send any prescriptions, order lab tests, or make referrals as outlined in your plan.

## Best Practices

To maximize the effectiveness of this skill and your teleconsultations, consider the following best practices.

- **Maintain Good Webside Manner:** Just as in person, your demeanor on camera matters. Maintain eye contact by looking at the camera, listen actively, and show empathy.
- **Be Prepared for Technology Failures:** Have a backup plan. If the video call fails, be ready to switch to a phone call to complete the consultation.
- **Ensure Patient Privacy:** Remind the patient to be in a private location. Use headphones to ensure your conversation is not overheard.
- **Set Clear Expectations:** At the start of the consultation, briefly explain the process and what can be accomplished. Also, clarify the limitations.
- **Encourage Patient Engagement:** Ask the patient to actively participate, for example, by showing you the location of their pain or demonstrating their range of motion.
- **Document Limitations:** Always note the limitations of the virtual examination in your clinical notes. This is crucial for medical-legal protection.

## Examples

Here are a few practical examples of how the Telemedicine Assistant can be used in a clinical setting.

**Example 1: Initial Consultation for Chronic Low Back Pain**

- **Action:** `telemedicine-assistant --action generate-checklist --patient "John Doe"`
- **Action:** `telemedicine-assistant --action generate-soap-note --complaint "Chronic low back pain"`
- **Workflow:** Dr. Bergamaschi initiates the skill. He reviews the generated checklist, prepares for the call, and sends the patient instructions. During the call, he obtains informed consent. He uses the SOAP note template to document John's 10-year history of back pain, his failed treatments, and his goals for regenerative medicine. The virtual exam includes assessing his range of motion. The plan involves ordering a new MRI and a follow-up teleconsultation to discuss the results and a potential prolotherapy treatment.

**Example 2: Follow-up for a Patient Post-PRP Injection**

- **Action:** `telemedicine-assistant --action generate-soap-note --complaint "Follow-up post-PRP for knee osteoarthritis"`
- **Workflow:** Sarah, a 65-year-old patient, had a PRP injection in her right knee two weeks ago. Dr. Bergamaschi uses the SOAP note template for the follow-up. Sarah reports a 50% reduction in pain and improved mobility. The virtual exam shows reduced swelling. The plan is to continue the prescribed physical therapy and have another follow-up in one month.

**Example 3: Consultation for Medical Cannabis**

- **Action:** `telemedicine-assistant --action generate-full-consult --patient "Jane Smith" --complaint "Neuropathic pain and anxiety"`
- **Workflow:** This single command could generate the checklist, consent, and SOAP note templates at once. Dr. Bergamaschi conducts a detailed consultation with Jane, who is seeking medical cannabis for neuropathic pain secondary to diabetes. He documents her extensive medical history, previous treatments, and her expectations. He educates her on the risks and benefits of cannabinoid therapy, documents the informed consent, and creates a detailed treatment plan, starting with a low dose of CBD oil. The plan includes regular follow-ups to monitor for efficacy and side effects.

## References

For further reading and to stay updated on the best practices in telemedicine, here are some valuable resources:

1.  **American Telemedicine Association (ATA):** [https://www.americantelemed.org/](https://www.americantelemed.org/) - A leading organization in telemedicine, providing resources, guidelines, and advocacy.
2.  **The World Health Organization (WHO) - Telemedicine:** [https://www.who.int/goe/publications/goe_telemedicine_2010.pdf](https://www.who.int/goe/publications/goe_telemedicine_2010.pdf) - A report on telemedicine, providing a global perspective on its use and implementation.
3.  **The Center for Connected Health Policy (CCHP):** [https://www.cchpca.org/](https://www.cchpca.org/) - A key resource for information on telehealth policy, reimbursement, and regulations in the United States.
4.  **Medical Board of California - Telehealth Guidelines:** [https://www.mbc.ca.gov/Licensing/Telehealth.aspx](https://www.mbc.ca.gov/Licensing/Telehealth.aspx) - An example of state-specific guidelines for the practice of telemedicine.
5.  **Evidence-Based Practice for Telemental Health:** [https://telehealth.org/ebp/](https://telehealth.org/ebp/) - While focused on mental health, this resource provides a great framework for evidence-based telehealth practice that can be adapted to other specialties.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

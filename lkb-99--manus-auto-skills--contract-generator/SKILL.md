---
name: contract-generator
description: Generate customized legal contracts using validated templates. Use this skill when users need to create agreements, NDAs, service contracts, or other legal documents. Triggers: contract, agreement, legal document, NDA, non-disclosure, service agreement, lease, terms of service, contrato, acordo, documento legal, distrato, aditivo contratual. Use when this capability is needed.
metadata:
  author: lkb-99
---

# Contract Generator

## Overview
The Contract Generator skill empowers Manus to create customized legal agreements by leveraging a library of pre-validated templates. This skill is designed to streamline the process of drafting common contracts, ensuring accuracy, compliance, and efficiency. By providing specific details and parameters, users can generate tailored contracts for various situations, reducing the need for manual drafting and minimizing legal risks. This skill is ideal for freelancers, small businesses, and individuals who require legally sound documents without the high cost of hiring a lawyer for standard agreements.

## Automatic Triggers

**ALWAYS activate this skill when a user mentions:**
- Keywords: contract, agreement, legal document, NDA, non-disclosure, service agreement, lease, terms of service, bill of sale, promissory note, SOW, statement of work, aditivo, distrato
- Palavras-chave: contrato, acordo, documento legal, termo de confidencialidade, contrato de serviço, contrato de aluguel, termos de serviço, recibo de venda, nota promissória, aditivo contratual, distrato social
- Phrases: "create a contract", "draft an agreement", "need an NDA", "generate a lease", "write terms of service"
- Frases: "criar um contrato", "elaborar um acordo", "preciso de um termo de confidencialidade", "gerar um contrato de aluguel", "escrever os termos de serviço"
- Context: Any discussion about drafting, creating, or generating formal legal agreements between parties.

**Example user queries that trigger this skill:**
- "I need to create a contract for a freelance project."
- "Can you help me draft a non-disclosure agreement?"
- "Preciso de um contrato de aluguel para um apartamento."
- "Como eu crio os termos de serviço para o meu site?"

## When to Use This Skill

**ALWAYS use this skill when the user wants to:**
- Generate a Non-Disclosure Agreement (NDA)
- Create an Independent Contractor or Service Agreement
- Draft a Residential or Commercial Lease Agreement
- Formalize a transaction with a Bill of Sale or Purchase Order
- Create website documents like Terms and Conditions or a Privacy Policy
- Draft personal agreements like a Promissory Note or Loan Agreement
- Create a Statement of Work (SOW)
- Elaborar um aditivo contratual ou um distrato social.

## Core Capabilities

### 1. Template-Based Generation
The skill utilizes a robust library of legal templates as a foundation for contract creation. These templates are curated and validated to ensure they meet legal standards for various jurisdictions.

*   **Template Library:** Access a wide range of templates, including:
    *   Non-Disclosure Agreement (NDA)
    *   Independent Contractor Agreement
    *   Service Agreement
    *   Residential Lease Agreement
    *   Website Terms and Conditions
    *   Privacy Policy
    *   Bill of Sale
    *   Promissory Note
*   **Customization:** Each template contains placeholders and variables that can be dynamically populated with user-provided information.

### 2. Dynamic Content Population
Users can provide specific details through prompts, which the skill then uses to fill in the blanks in the chosen template. This ensures that each contract is tailored to the specific needs of the user.

*   **Variable Injection:** The skill identifies variables such as `[Client Name]`, `[Service Description]`, `[Payment Amount]`, and `[Effective Date]` and replaces them with the correct information.
*   **Clause Selection:** Users can choose to include or exclude optional clauses based on their requirements. For example, a service agreement might have optional clauses for intellectual property rights or a non-compete agreement.

### 3. Output Formatting
The generated contract can be produced in various formats for ease of use and distribution.

*   **Markdown:** The default output format is a clean, readable Markdown file.
*   **PDF Conversion:** The skill can convert the final Markdown document into a professional-looking PDF file, ready for signing.
*   **Plain Text:** A simple text version can also be generated for easy copying and pasting into other applications.

### 4. Validation and Review
The skill includes a step for reviewing the generated contract before finalizing it. This allows the user to double-check all the details and make any necessary corrections.

*   **Preview Mode:** Before generating the final file, the skill can display a preview of the contract with all the populated data.
*   **Editing Loop:** If any changes are needed, the user can provide new information or corrections, and the skill will regenerate the contract.

## Step-by-Step Workflow

1.  **Initiate the Skill:** Start by invoking the `contract-generator` skill.
2.  **Select a Template:** Manus will ask you to choose a contract template from the available library.
    *   **Example Prompt:** "Which type of contract would you like to generate? Available options are: NDA, Service Agreement, Lease Agreement..."
3.  **Provide Contract Details:** Manus will then prompt you for the necessary information to populate the template. This will be a series of questions.
    *   **Example Interaction (for a Service Agreement):**
        *   **Manus:** "What is the full name of the client?"
        *   **User:** "Innovate Corp."
        *   **Manus:** "Please provide a detailed description of the services to be rendered."
        *   **User:** "Development of a new e-commerce website with payment gateway integration."
        *   **Manus:** "What is the total payment amount?"
        *   **User:** "$10,000"
4.  **Review the Draft:** Once all the information is collected, Manus will generate a draft of the contract and present it for your review.
5.  **Request Edits (Optional):** If you spot any errors or want to make changes, you can instruct Manus to edit the document.
    *   **Example Prompt:** "Change the payment amount to $12,000 and add a clause for a 50% upfront payment."
6.  **Finalize and Export:** After you approve the draft, Manus will generate the final contract in your desired format (Markdown, PDF, or TXT).

## Best Practices

*   **Be Specific:** Provide clear and detailed information when prompted. Ambiguity can lead to a weak or unenforceable contract.
*   **Review Carefully:** Always read through the generated contract thoroughly before signing or sending it. Ensure all names, dates, and amounts are correct.
*   **Consult a Lawyer:** While this skill is a powerful tool, it is not a substitute for legal advice. For complex or high-stakes agreements, it is always best to have the contract reviewed by a qualified legal professional.
*   **Keep Records:** Save a copy of all generated contracts for your records. Digital signatures can be added using external tools.
*   **Jurisdiction:** Be mindful of the legal jurisdiction. The default templates are general, but some clauses may need to be adjusted to comply with local laws.

## Examples

### Example 1: Generating a Non-Disclosure Agreement (NDA)

**User:** "Manus, I need to create an NDA."

**Manus:** "Of course. I will use the standard Non-Disclosure Agreement template. Please provide the following information:
1.  Full name of the Disclosing Party.
2.  Full name of the Receiving Party.
3.  The effective date of the agreement.
4.  A description of the confidential information."

**User:**
1.  "My company, 'Innovate Forward Inc.'"
2.  "A potential partner, 'Synergy Solutions LLC.'"
3.  "Today's date."
4.  "Our proprietary algorithm for data analysis and related business strategies."

**(Manus generates the NDA and presents it for review)**

### Example 2: Creating a Freelance Service Agreement

**User:** "I need a service agreement for a freelance design project."

**Manus:** "Understood. I will use the Service Agreement template. Let's go through the details. What is the client's name? ..."

**(Manus proceeds to ask for all the necessary details, such as scope of work, payment terms, timeline, and ownership of intellectual property.)**

## Templates

Here are a few sample templates that the skill can use as a base. Note that these are simplified versions for illustration.

### Template 1: Non-Disclosure Agreement (NDA)

```markdown
# Non-Disclosure Agreement

This Non-Disclosure Agreement (the "Agreement") is entered into as of [Effective Date] by and between [Disclosing Party Name] ("Disclosing Party") and [Receiving Party Name] ("Receiving Party").

## 1. Confidential Information

Disclosing Party has disclosed or may disclose to Receiving Party certain confidential information, which includes but is not limited to: [Description of Confidential Information].

## 2. Obligations of Receiving Party

The Receiving Party agrees to hold the Confidential Information in strict confidence and to take all reasonable precautions to protect such Confidential Information.

## 3. Term

This Agreement shall remain in effect for a period of [Term of Agreement, e.g., 'three years'] from the Effective Date.

IN WITNESS WHEREOF, the parties have executed this Agreement as of the Effective Date.

**Disclosing Party:**
[Disclosing Party Name]

**Receiving Party:**
[Receiving Party Name]
```

### Template 2: Simple Service Agreement

```markdown
# Service Agreement

This Service Agreement (the "Agreement") is made effective as of [Effective Date], by and between [Client Name] ("Client") and [Service Provider Name] ("Service Provider").

## 1. Services

Service Provider agrees to provide the following services to the Client: [Detailed Description of Services].

## 2. Payment

Client agrees to pay Service Provider the total amount of [Payment Amount] for the services. Payment shall be made as follows: [Payment Schedule, e.g., '50% upon signing, 50% upon completion'].

## 3. Term

This Agreement will begin on [Start Date] and will remain in effect until the services are completed, which is expected to be by [End Date].

## 4. Governing Law

This Agreement shall be governed by the laws of the State of [State/Jurisdiction].

**Client:**
[Client Name]

**Service Provider:**
[Service Provider Name]
```

## References

*   [Rocket Lawyer: Create Legal Documents](https://www.rocketlawyer.com/)
*   [LegalZoom: Legal Forms & Services](https://www.legalzoom.com/)
*   [Nolo: Law for All](https://www.nolo.com/)
*   [Harvard Law School: Sample Agreements](https://hls.harvard.edu/dept/opia/sample-agreements/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

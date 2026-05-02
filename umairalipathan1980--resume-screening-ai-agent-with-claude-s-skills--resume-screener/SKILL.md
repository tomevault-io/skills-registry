---
name: resume-screener
description: Systematic workflow for screening and evaluating candidate resumes against job requirements. This skill provides a structured, validation-based approach to ensure consistent, objective, and high-quality candidate assessments. Use when this capability is needed.
metadata:
  author: umairalipathan1980
---

# Resume Screening Skill

## Overview

This skill provides a structured workflow for screening candidate resumes with validation to ensure evaluation quality and consistency. It is designed to be reusable across any position, industry, or evaluation methodology.

## When to Use This Skill

Use this skill when you need to:
- Screen job applicants and evaluate resumes
- Provide structured candidate assessments
- Generate hiring recommendations with detailed justifications
- Ensure consistent evaluation across multiple candidates

**Trigger phrases:**
- "Screen these resumes"
- "Evaluate these candidates"
- "Review these job applications"
- "Assess candidate qualifications"

## Required User Inputs

Before starting the screening workflow, gather these essential inputs:

1. **Job Description** (REQUIRED): The complete job posting including requirements, qualifications, responsibilities, and company information (`inputs/job_description.pdf`).
2. **Candidate Resumes** (REQUIRED): Resume files submitted by candidates (PDF or DOCX format) (`../resumes/` folder)
3. **Evaluation Criteria** (OPTIONAL): Detailed scoring rubric and assessment guidelines. If not provided, use standard industry practices (`inputs/evaluation_criteria.pdf`).
4. **Screening Template** (OPTIONAL): Standardized format for presenting evaluation results. If not provided, create a clear, professional format (`inputs/screening_template.docx`).

**IMPORTANT:** If job description or resumes are missing, request them from the user before proceeding.

## Resume Screening Workflow

Process resumes one by one and repeat Steps 1-5 for each candidate. Follow this workflow precisely to ensure thorough and validated evaluations.

### Step 1: Read and Understand Context
The input and resumes directories are in `C:/Users/h02317/skills` folder. 

Before evaluating, read all provided materials:
- Review the job description to understand role requirements, qualifications, and responsibilities (`inputs/job_description.pdf`).
- Review the evaluation criteria (if provided) to understand scoring methodology (`inputs/evaluation_criteria.pdf`).
- Review the screening template (if provided) to understand output format expectations (`inputs/screening_template.docx`).
- Read the current candidate's resume thoroughly (`resumes/` folder)
 
**Extract key information:**
- Required qualifications (must-haves)
- Preferred qualifications (nice-to-haves)
- Scoring categories and point allocations (from evaluation criteria)
- Special considerations or red flags to watch for

### Step 2: Extract and Assess Candidate Information

For the current candidate, systematically extract and evaluate:

- **Contact Information**: Name, email, phone, location, LinkedIn, GitHub
- **Education**: Degrees, institutions, graduation years, relevant coursework, GPA if mentioned
- **Professional Experience**: Job titles, companies, dates, responsibilities, achievements, quantifiable results
- **Technical Skills**: Programming languages, frameworks, tools, certifications
- **Projects & Publications**: Relevant projects, research, open-source contributions
- **Other Qualifications**: Certifications, awards, languages, volunteer work

Assess each area against job requirements (`inputs/job_description.pdf`) using the evaluation criteria (`inputs/evaluation_criteria.pdf`).

**Assessment Approach:**
- Compare each area against job requirements
- Note specific evidence from the resume
- Identify strengths and gaps
- Look for quantifiable achievements and impact metrics
- Consider career progression and growth trajectory

**CRITICAL:** Base assessment solely on resume content. Do not make assumptions about information not present. If information is missing, note it as 'Not Available' or 'Not Mentioned'.

### Step 3: Generate Initial Evaluation

Create an evaluation following the structure defined in the screening template (`inputs/screening_template.docx`) (if provided).
**VERY IMPORTANT**: If a screening template is provided, strictly follow its structure. 

**Standard evaluation should include:**

1. **Scoring**: Assign points according to the evaluation criteria (`inputs/evaluation_criteria.pdf`) for each category
   - If no criteria provided, use industry-standard categories (e.g., Technical Skills, Experience, Education, Communication, Additional Factors)
   - Provide point-by-point justification based on specific resume content

2. **Strengths Analysis**: Identify 3-5 key strengths with specific examples from the resume

3. **Concerns Analysis**: Identify 2-3 key concerns or gaps with specific examples

4. **Summary**: 
   - Total score (if using a scoring system)
   - Overall assessment (2-3 sentences)
   - Fit analysis for the specific role

5. **Recommendation**: 
   - Clear recommendation (e.g., Strong Interview / Interview / Maybe / Reject)
   - Suggested next steps (e.g., Phone screen / Technical assessment / Panel interview)
   - Interview focus areas (topics to probe during interview)

**Evaluation Principles:**
- Be objective and evidence-based
- Use specific examples from the resume
- Avoid assumptions or speculation
- Be consistent across all candidates
- Focus on job-relevant qualifications only

### Step 4: Validate the Evaluation

**CRITICAL:** Immediately after generating the evaluation, validate it against the job requirements and evaluation criteria.

**Validation checklist:**
- ☐ **Completeness**: All required evaluation sections addressed
- ☐ **Accuracy**: Scores align with evidence from resume; no invented or assumed information
- ☐ **Consistency**: Similar qualifications scored similarly across candidates
- ☐ **Objectivity**: Evaluation based on facts, not assumptions, bias, or protected characteristics
- ☐ **Clarity**: Justifications are specific and reference actual resume content
- ☐ **Alignment**: Recommendation matches the scoring and detailed assessment
- ☐ **Format**: Output format matches the screening template (`inputs/evaluation_criteria.pdf`) (if provided)

**Specific validation checks:**
- Verify each score has supporting evidence from the resume
- Confirm no information was fabricated or assumed
- Check that scoring is proportional (higher scores require stronger evidence)
- Ensure consistency with previous evaluations (if applicable)
- Verify recommendation logically follows from the scores

### Step 5: Address Validation Failures

If validation identifies issues:

1. **Review**: Carefully examine the validation feedback to understand specific problems
2. **Identify**: Determine which sections need correction (scores, justifications, recommendations)
3. **Correct**: Make targeted revisions to address only the identified issues
4. **Re-validate**: Execute Step 4 again to verify corrections
5. **Iterate**: Repeat if needed (maximum 3 rounds)

**Do NOT proceed to Step 6 until validation passes.**

If validation fails after 3 rounds, stop and request user assistance.

### Step 6: Save Evaluation & Validation Results

Only when validation passes successfully:

1. **Format the evaluation:**
   - If a screening template is provided, follow its structure strictly
   - If no template provided, use a clear, professional format with all required sections

2. **Document validation:**
   - Immediately following each evaluation, append a brief validation summary:
     - Round 1: [PASSED/FAILED] - [Brief description]
     - Round 2 (if applicable): [PASSED/FAILED] - [Description]
     - Round 3 (if applicable): [PASSED/FAILED] - [Description]

3. **Save the output:**
   - Save to `candidate_evaluations.docx` (or specified output file). Use `docx` skill to generate the docx document. If this skill is not available, use `python-docx` package to generate the docx document. Validate that the docx document has been correctly generated. 
   - Add all candidate evaluations to the SAME file
   - Use clear separators or page breaks between each candidate
   - Remove any temporary scripts created during the workflow. 

## Directory Structure

The skill expects this flexible directory structure (adapt as needed):

```
skills/
├── inputs/
│   ├── job_description.[pdf|docx|txt]     # Job posting (required)
│   ├── evaluation_criteria.[pdf|docx]     # Scoring rubric (optional)
│   └── screening_template.docx            # Output template (optional)
├── resumes/
│   ├── candidate1_resume.[pdf|docx]       # Candidate resumes (required)
│   ├── candidate2_resume.[pdf|docx]
│   └── ...
└── candidate_evaluations.docx             # Final output (generated)
```

## Quality Assurance Principles

The validation step ensures:
- **Objectivity**: Evaluations based on factual resume content only
- **Consistency**: Similar qualifications scored similarly across all candidates
- **Completeness**: All evaluation criteria addressed for each candidate
- **Fairness**: No bias based on protected characteristics (age, gender, race, etc.)
- **Accuracy**: Scores and justifications align with actual resume evidence
- **Transparency**: Clear reasoning for all scores and recommendations

**Never skip validation**, even for candidates who are clearly qualified or unqualified.

## Best Practices

1. **Read thoroughly**: Review entire resume before scoring
2. **Be specific**: Reference exact skills, experiences, or achievements
3. **Be objective**: Base all assessments on observable facts
4. **Be consistent**: Apply the same standards to all candidates
5. **Be complete**: Address all evaluation criteria for each candidate
6. **Be fair**: Avoid assumptions or bias; focus only on job-relevant qualifications
7. **Validate always**: Never skip the validation step
8. **Document clearly**: Provide clear justifications for all scores and recommendations

## Common Red Flags

Watch for these potential concerns (note them objectively in evaluation):
- Frequent job changes (< 1 year at multiple positions) without clear progression
- Significant unexplained employment gaps
- Lack of quantifiable achievements or impact metrics
- Missing fundamental requirements clearly stated in job description
- Vague or inflated descriptions without concrete details
- Poor resume quality (significant typos, formatting issues, unclear structure)
- Inconsistencies in dates, titles, or responsibilities

## Positive Indicators

Note these strengths when present:
- Clear career progression and growth
- Quantifiable achievements with specific metrics
- Relevant industry experience
- Strong educational background aligned with role
- Active learning (recent certifications, courses, publications)
- Leadership and mentoring experience
- Open-source contributions or community involvement
- Evidence of impact and business results

## Troubleshooting

**Issue**: Validation repeatedly fails
- **Solution**: Review error messages systematically and address each issue. If validation fails after 3 rounds, stop and request user assistance.

**Issue**: Resume file not found or unreadable
- **Solution**: Verify file exists in correct folder and is in readable format (PDF or DOCX). Check file permissions.

**Issue**: Evaluation feels incomplete or biased
- **Solution**: Re-read job requirements and evaluation criteria. Ensure all scores are justified with specific resume content. Remove any assumptions.

**Issue**: Inconsistent scoring across candidates
- **Solution**: Review previous evaluations to ensure similar qualifications are scored similarly. Adjust if needed.

**Issue**: No evaluation criteria provided
- **Solution**: Create a standard evaluation framework based on job requirements, or ask user for guidance.

## Ethical Considerations

This skill promotes fair and objective candidate screening. When using this skill:

- **Avoid bias**: Do not consider protected characteristics (age, gender, race, religion, etc.)
- **Focus on qualifications**: Evaluate only job-relevant skills and experience
- **Be transparent**: Provide clear justifications for all decisions
- **Maintain consistency**: Apply same standards to all candidates
- **Protect privacy**: Handle candidate information confidentially
- **Human oversight**: Remember this skill assists human decision-making; final hiring decisions should involve human judgment, interviews, and additional assessments

## Adaptability

This skill is designed to be flexible and reusable:
- Works with any job position or industry
- Adapts to custom evaluation criteria
- Supports various output formats
- Scales from individual contributor to executive roles
- Can be customized with organization-specific requirements

Simply provide the job description, resumes, and optionally your evaluation criteria and template to begin screening.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/umairalipathan1980) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

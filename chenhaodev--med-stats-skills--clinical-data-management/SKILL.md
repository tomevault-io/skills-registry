---
name: clinical-data-management
description: | Use when this capability is needed.
metadata:
  author: chenhaodev
---

# Clinical Data Management

## Quick Reference

| Phase | SOP/Example | Key Activities | Reference |
|-------|-------------|----------------|-----------|
| **Interim Analysis** | SOP | Protocol, statistical stopping rules, reporting | [SOP](references/interim-analysis-report.md) |
| **Cohort Splitting** | Data Source Utility Plan | Training/validation split, stratification | [SOP](references/data-source-utility-cohort-split.md) |
| **Exploratory Analysis** | Study Data Explore/Develop | Data profiling, feature exploration, visualization | [SOP](references/study-data-explore-develop.md) |
| **Trial Design Example** | Heart Failure | AIMPower trial - remote monitoring protocol | [Example](references/trial-protocol-aimpower-heart-failure.md) |
| **Trial Design Example** | Cancer Care | CART trial - remote monitoring in oncology | [Example](references/trial-protocol-cart-cancer-remote-monitoring.md) |

## When to Use

Use this skill when you are involved in the lifecycle of clinical trial data, specifically:

*   **Planning Data Strategy**: Defining how data sources will be utilized and cohorts defined (Data Source Utility).
*   **Exploratory Phase**: Conducting initial data profiling, quality checks, and hypothesis generation (Study Data Explore/Develop).
*   **Interim Monitoring**: Performing formal interim analyses to assess safety, efficacy, or futility during an ongoing trial.
*   **Trial Design**: Structuring data management plans for digital health interventions (e.g., remote monitoring).
*   **Predictive Modeling**: Preparing clinical datasets for machine learning, including rigorous training/validation splitting.

## How to Use

1.  **Identify the Current Phase**: Determine if you are in the planning, exploration, or monitoring phase of the clinical trial.
2.  **Select the Appropriate SOP**: Use the Quick Reference table to find the Standard Operating Procedure (SOP) that matches your current task.
3.  **Review Real-World Examples**: If designing a new protocol, especially for digital health, consult the AIMPower (Heart Failure) and CART (Cancer) examples for structural guidance.
4.  **Apply Statistical Rigor**: Use the linked Shared Resources to ensure appropriate statistical tests are selected for your analysis plan.

## Data Preparation SOPs

This section details the Standard Operating Procedures for critical data management tasks.

### Interim Analysis Reporting
**File**: [references/interim-analysis-report.md](references/interim-analysis-report.md)

Guidelines for conducting and reporting interim analyses. This SOP covers:
*   Establishing the Data Safety Monitoring Board (DSMB) charter.
*   Defining statistical stopping rules (e.g., O'Brien-Fleming boundaries).
*   Structuring the interim analysis report to maintain blinding where necessary.

### Data Source Utility & Cohort Splitting
**File**: [references/data-source-utility-cohort-split.md](references/data-source-utility-cohort-split.md)

Methodology for defining data utility and splitting cohorts for analysis. This SOP covers:
*   **Data Source Utility Plan (DSUP)**: Documenting the provenance and intended use of each data source.
*   **Cohort Splitting**: Strategies for creating training, validation, and test sets, ensuring balanced stratification of key clinical variables.

### Study Data Exploration & Development
**File**: [references/study-data-explore-develop.md](references/study-data-explore-develop.md)

A framework for Exploratory Data Analysis (EDA) in clinical research. This SOP covers:
*   Data profiling and quality assessment (missingness, outliers).
*   Univariate and bivariate analysis to understand distributions and relationships.
*   Feature engineering and selection for downstream analysis.

## Digital Health Trial Examples

Real-world examples of clinical trial protocols focusing on digital health and remote monitoring.

### Heart Failure Remote Monitoring (AIMPower)
**File**: [references/trial-protocol-aimpower-heart-failure.md](references/trial-protocol-aimpower-heart-failure.md)

An example protocol for the AIMPower trial, demonstrating:
*   Integration of remote monitoring devices in heart failure management.
*   Data collection schedules and patient compliance tracking.
*   Endpoints related to readmission reduction and quality of life.

### Cancer Care Remote Monitoring (CART)
**File**: [references/trial-protocol-cart-cancer-remote-monitoring.md](references/trial-protocol-cart-cancer-remote-monitoring.md)

An example protocol for the CART trial, illustrating:
*   Remote symptom monitoring for cancer patients undergoing treatment.
*   Alerting algorithms for clinical decision support.
*   Implementation of patient-reported outcomes (PROs) in a digital workflow.

## Shared Resources

*   **[Statistical Test Selection Guide](../shared/statistical-test-selection.md)**: Decision trees and criteria for choosing appropriate statistical tests for clinical trial data analysis. Essential for validating the statistical analysis plan (SAP) associated with these data management activities.

## References

*   **SOPs**: Derived from standard clinical data management practices and adapted for digital health contexts.
*   **Trial Examples**: Based on the AIMPower and CART study protocols.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chenhaodev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

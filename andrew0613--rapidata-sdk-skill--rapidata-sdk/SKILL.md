---
name: rapidata-sdk
description: Use when working with the Rapidata Python SDK to create or manage audiences, job definitions, and jobs for human labeling (image, video, audio, or text), configure early stopping, interpret results, or run Model Ranking Insights (MRI) benchmarks.
metadata:
  author: andrew0613
---

# Rapidata SDK

## Overview
Use this skill to plan and execute Rapidata labeling workflows with the Python SDK, including compare or classification jobs, audience curation, and MRI benchmarks.

## Workflow Decision Tree
- Use compare jobs for pairwise preference or quality comparisons.
- Use classification jobs for single-item labeling with fixed options.
- Use MRI when ranking multiple models across prompts with leaderboards.

## Core Workflow
1. Initialize client and authenticate. See `references/quickstart.md`.
2. Choose an audience.
   - Use global audience for fast experiments.
   - Use custom audience for higher quality by adding qualification examples.
   - See `references/audiences.md`.
3. Define the job.
   - Pick compare vs classification, set datapoints and contexts.
   - Add settings like `NoShuffle`, `Markdown`, or `AllowNeitherBoth` when needed.
   - See `references/job_definitions.md`.
4. Preview and run.
   - Call `job_definition.preview()`.
   - Assign with `audience.assign_job()` and monitor with `job.display_progress_bar()`.
5. Retrieve and interpret results. See `references/results.md`.

## Quality and Cost Controls
- Use confidence-based early stopping for clear-cut tasks. See `references/early_stopping.md`.
- Write concise, unambiguous instructions to reduce labeler confusion. See `references/prompting.md`.

## Migration (Order API -> Audience/Job)
- Migrate legacy Order/Validation flows to Audience + Job Definition.
- See `references/migration.md` for side-by-side mapping.

## MRI (Model Ranking Insights)
- Create benchmarks, leaderboards, and evaluate models with prompts or identifiers.
- Configure inverse ranking, level of detail, and prompt display as needed.
- See `references/mri.md`.

## Resources

### references/
- `references/quickstart.md` - install, auth, and core job workflow.
- `references/audiences.md` - global vs custom audiences and qualification examples.
- `references/job_definitions.md` - parameters, settings, and compare/classification examples.
- `references/results.md` - result structure and userScore meaning.
- `references/prompting.md` - instruction design best practices.
- `references/early_stopping.md` - confidence threshold usage and result fields.
- `references/mri.md` - MRI benchmark and leaderboard workflows.
- `references/config.md` - logging and upload configuration entry points.
- `references/migration.md` - legacy Order API migration guide.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andrew0613) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

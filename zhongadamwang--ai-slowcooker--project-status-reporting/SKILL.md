---
name: project-status-reporting
description: Generate comprehensive project status reports, executive dashboards, and stakeholder communications with automatic data aggregation from project documentation structure. Use when this capability is needed.
metadata:
  author: zhongadamwang
---

# Project Status Reporting

Generates standardized status reports, executive dashboards, and stakeholder communications by aggregating data from project documentation structures and tracking artifacts.

## Intent

Aggregate status data from EDPS project documentation artifacts (task tracking, project plans, milestone data) and generate audience-appropriate reports — executive dashboards, engineering detail, or stakeholder briefings — with minimal manual data entry.

## Inputs

- **Project root**: Path to `OrgDocument/projects/[NN] - [Project Name]/`
- **Optional**: `tasks/task-tracking.md` — task completion data
- **Optional**: `project-plan.md` — milestone schedule baseline
- **Report type**: `executive` | `engineering` | `stakeholder` | `full` (default: `full`)

## Outputs

- Formatted status report Markdown file appropriate for the requested audience
- Console summary: overall completion percentage, count of blocked items, and upcoming milestones

## Core Function

**Input**: Project data from document tree, tracking files, milestone status
**Output**: Formatted reports, dashboards, presentations for various stakeholder audiences

## Report Types

### 1. Executive Summary Report

**Audience**: Senior leadership, sponsors
**Frequency**: Monthly or milestone-based
**Format**: High-level, visual, action-oriented

```markdown
# Executive Project Report - [Project Name]
**Report Date**: [Date]
**Project Manager**: [Name]
**Sponsor**: [Name]

## 🎯 Executive Summary
- **Overall Status**: [🟢 On Track | 🟡 At Risk | 🔴 Behind]
- **Budget Status**: [% used] of [total budget] ([🟢 Under | 🟡 On Track | 🔴 Over])
- **Timeline**: [On Schedule | X days behind | X days ahead]
- **Next Major Milestone**: [Milestone] - [Date]

## 📊 Key Metrics
| Metric | Current | Target | Trend |
|--------|---------|--------|-------|
| Scope Complete | [X%] | [target%] | [↗↗↘] |
| Budget Used | [X%] | <80% | [↗↗↘] |
| Schedule Adherence | [X%] | >90% | [↗↗↘] |

## 🚨 Attention Required
### Critical Issues (Red)
- [Issue requiring executive intervention]

### Significant Risks (Yellow)  
- [Risk requiring sponsor awareness]

## 🎉 Major Accomplishments
- [Key achievement this period]
- [Milestone completed]

## 📅 Next Period Focus
- [Key deliverable]
- [Important milestone]

## 💰 Financial Summary
- **Budget Approved**: [amount]
- **Spent to Date**: [amount] ([%])
- **Forecast to Complete**: [amount]
- **Variance**: [+/- amount]
```

### 2. Detailed Project Status Report

**Audience**: Project team, stakeholders
**Frequency**: Weekly/bi-weekly  
**Format**: Comprehensive, detailed tracking

```markdown
# Project Status Report - [Project Name]
**Period**: [Start Date] - [End Date]
**Prepared by**: [PM Name]
**Distribution**: [Stakeholder list]

## Project Overview
- **Project**: [Name and description]
- **Phase**: [Current phase]
- **Start Date**: [Date]
- **Target Completion**: [Date]
- **Current Status**: [🟢🟡🔴]

## Scope Progress
### Requirements Status
- **Total Requirements**: [count]
- **Approved**: [count] ([%])
- **In Review**: [count] ([%])
- **Draft**: [count] ([%])

### Deliverables Status
| Deliverable | Owner | Target Date | Status | % Complete |
|-------------|-------|-------------|---------|------------|
| [Item] | [Name] | [Date] | [🟢🟡🔴] | [%] |

## Schedule Status
### Current Milestone: [Name]
- **Target Date**: [Date]
- **Forecast Date**: [Date]
- **Status**: [On Track/At Risk/Behind]
- **Completion**: [%]

### Upcoming Milestones (Next 30 days)
| Milestone | Target Date | Owner | Risk Level |
|-----------|-------------|-------|------------|
| [Name] | [Date] | [Owner] | [🟢🟡🔴] |

## Issue Register
### Open Issues
| ID | Description | Priority | Owner | Target Resolution |
|----|-------------|----------|-------|-------------------|
| I-001 | [Issue] | [H/M/L] | [Name] | [Date] |

### Resolved This Period
- [Issue resolved] - [Resolution date]

## Risk Assessment
### Active Risks
| ID | Risk Description | Probability | Impact | Mitigation | Owner |
|----|-----------------|-------------|---------|-------------|-------|
| R-001 | [Risk] | [H/M/L] | [H/M/L] | [Action] | [Name] |

## Resource Status
### Team Utilization
- **Available**: [hours/FTEs]
- **Allocated**: [hours/FTEs] ([%])
- **Overcommitted**: [Yes/No]

### Budget Status  
- **Approved Budget**: [amount]
- **Committed**: [amount] ([%])
- **Remaining**: [amount]
- **Burn Rate**: [amount/period]
- **Forecast at Completion**: [amount]

## Quality Metrics
- **Test Cases**: [passed]/[total] ([% pass rate])
- **Defects**: [open count] ([priority breakdown])
- **Code Coverage**: [%] (Target: [%])
- **Review Completion**: [%]

## Change Requests
### Submitted This Period
| CR ID | Description | Impact | Status | Decision Date |
|-------|-------------|---------|---------|--------------|
| CR-001 | [Change] | [S/T/B] | [Pending/Approved/Rejected] | [Date] |

### Approved Changes  
- [Change description] - Impact: [description]

## Next Period Plan
### Key Activities
- [Activity 1] - [Owner] - [Target date]
- [Activity 2] - [Owner] - [Target date]

### Decisions Needed
- [Decision required] - [By whom] - [By when]

## Appendix
### Team Roster
| Name | Role | Allocation | Contact |
|------|------|------------|---------|
| [Name] | [Role] | [%] | [Email] |
```

### 3. Stakeholder Communication Brief

**Audience**: Business stakeholders, end users
**Frequency**: Milestone-based or monthly
**Format**: Business-focused, minimal technical detail

```markdown
# [Project Name] - Stakeholder Update
**Update #**: [Number]
**Date**: [Date]

## What We're Building
[Brief, business-focused project description]

## Progress Highlights
- ✅ [Business benefit delivered]
- ✅ [User-facing feature completed]  
- ✅ [Milestone achieved]

## What's Coming Next
### This Month
- [User-visible deliverable]
- [Business capability]

### Next Milestone: [Name] - [Date]
[What stakeholders will see/experience]

## How This Benefits You
- [Specific benefit 1]
- [Specific benefit 2]
- [Process improvement]

## What We Need From You
- [Action item for stakeholders]
- [Decision/input required]  
- [Testing/feedback opportunity]

## Questions?
Contact: [PM Name] - [Email] - [Phone]
Project Portal: [Link if applicable]
```

## Report Generation Process

### 1. Data Collection
```markdown
## Automatic Data Sources
- `project-milestones.md` → Schedule status
- `project-tasks.md` → Task completion
- `project-health.md` → KPI metrics  
- `requirements/` → Scope progress
- `test-cases/` → Quality metrics
- `status-reports/` → Historical trends

## Manual Inputs Required
- Issue descriptions and impact
- Risk assessments and mitigation plans
- Stakeholder feedback and decisions
- Budget and resource updates
- Qualitative observations
```

### 2. Report Assembly Workflow
1. **Collect Data**: Aggregate from project files and tracking systems
2. **Calculate Metrics**: Compute percentages, trends, variances
3. **Assess Status**: Apply red/yellow/green status indicators
4. **Generate Narrative**: Create executive summary and key messages
5. **Format Output**: Apply appropriate template for audience
6. **Review & Validate**: Check accuracy and completeness
7. **Distribute**: Send to stakeholder distribution lists

## Visual Dashboard Components

### Project Health Dashboard
```markdown
## 📊 Project Dashboard - [Project Name]

### Status Indicators
- **Overall Health**: [🟢🟡🔴]
- **Schedule**: [🟢🟡🔴] [X days ahead/behind]
- **Budget**: [🟢🟡🔴] [X% used]
- **Scope**: [🟢🟡🔴] [X% complete]
- **Quality**: [🟢🟡🔴] [X% pass rate]

### Progress Bars
Requirements: [████████░░] 80%
Development: [██████░░░░] 60%  
Testing: [███░░░░░░░] 30%
Documentation: [██████████] 100%

### Trend Charts (Last 8 Weeks)
Week:        W1  W2  W3  W4  W5  W6  W7  W8
Schedule:    🟢  🟢  🟡  🟡  🟡  🔴  🔴  🟡
Budget:      🟢  🟢  🟢  🟢  🟡  🟡  🟡  🟡
Quality:     🟢  🟢  🟢  🟢  🟢  🟢  🟡  🟢
```

### Milestone Timeline
```markdown
## 🗓️ Milestone Timeline

Past Milestones:
✅ M1: Requirements [Completed 2024-01-15]
✅ M2: Analysis [Completed 2024-02-28] 
✅ M3: Design [Completed 2024-04-10]

Current Milestone:
🟡 M4: Development [Target: 2024-06-15, Forecast: 2024-06-22]

Future Milestones:
⏳ M5: Testing [Target: 2024-07-30]
⏳ M6: Deployment [Target: 2024-08-15]
```

## Report Distribution

### Automatic Distribution Lists
```markdown
## Executive Reports
- Project Sponsor
- Program Manager
- Department Head
- Steering Committee

## Detailed Status Reports  
- Project Team Members
- Technical Stakeholders
- Business Analysts
- Quality Assurance

## Stakeholder Communications
- End User Representatives
- Business Process Owners
- Training Team
- Support Team
```

### Communication Channels
- **Email Distribution**: Automated sending to distribution lists
- **Project Portal**: Web-based dashboard and report repository
- **Team Meetings**: Presentation format for status meetings
- **Executive Briefings**: Slide deck format for leadership reviews

## Customization Guidelines

### Report Frequency by Project Type
- **Critical/High Visibility**: Weekly detailed, bi-weekly executive
- **Standard Projects**: Bi-weekly detailed, monthly executive  
- **Maintenance/Small**: Monthly detailed, quarterly executive

### Audience-Specific Adaptations
- **Technical Stakeholders**: Include detailed metrics, code quality, architecture decisions
- **Business Stakeholders**: Focus on business value, user impact, timeline
- **Executive Leadership**: High-level status, financial impact, risk mitigation
- **End Users**: Feature delivery, training timeline, support preparation

## Integration Points

- Reads data from `project-planning-tracking` skill artifacts
- Uses `project-document-management` folder structure
- Coordinates with `requirements-ingest` for scope metrics
- Supports `goals-extract` output for success criteria tracking

## Report Quality Standards

1. **Accuracy**: All data verified and current
2. **Timeliness**: Reports delivered on schedule
3. **Clarity**: Clear status indicators and actionable information
4. **Consistency**: Standard formats and terminology
5. **Completeness**: All required sections populated
6. **Relevance**: Content tailored to audience needs

---

## GitHub-Integrated Status Reporting

### Modern Data Sources

For projects using GitHub-integrated task management structure:

#### Primary Data Sources
- `tasks/task-tracking.md` → Current progress metrics
- `tasks/T##-task-name.md` → Individual task status
- `project-plan.md` → PERT analysis and critical path  
- `tasks/README.md` → Task management workflow status
- GitHub Issues API → Real-time collaboration data

#### Enhanced Status Report Template

```markdown
# Project Status Report - [Project Name]
**Period**: [Start Date] - [End Date]
**Report Date**: [Date]
**Project Manager**: [Name]

## Executive Summary
- **Overall Status**: [🟢 On Track | 🟡 At Risk | 🔴 Behind]
- **Phase**: [Current phase from project plan]
- **Critical Path Status**: [From PERT analysis]
- **GitHub Integration**: [Active/Synced/Issues tracking]

## Progress Metrics
### Task Completion (from task-tracking.md)
- **Phase 1**: [X]/[Total] tasks complete ([X]%)
- **Overall Project**: [X]/[Total] tasks complete ([X]%)
- **Critical Path**: [On schedule/X days behind/ahead]

### Team Collaboration (from GitHub Issues)
- **Active Issues**: [Count] 
- **Comments This Period**: [Count]
- **External Feedback**: [Quality/volume assessment]

## Phase Status
### Phase 1: [Phase Name] 
**Status**: [In Progress/Complete]
**Key Accomplishments**:
- [Completed task from T## files]
- [Another accomplishment]

**Active Work**:
- T##: [Task name] - [Owner] - [Status] - [Due date]

**Upcoming** (Next Period):
- T##: [Next task] - [Dependencies] - [Start date]

## Integration Health
### GitHub Sync Status
- **Last Sync**: [Date/time]
- **Sync Issues**: [None/Description if any]
- **Team Participation**: [Active contributors count]

### Documentation Alignment
- **Project Plan**: [Aligned/Needs update]
- **Task Dependencies**: [Validated/Review needed]
- **Requirements Traceability**: [Current/Outdated]

## Risks & Issues
| ID | Issue | Impact | GitHub Issue | Owner | Target Resolution |
|----|-------|---------|---------------|-------|-------------------|
| R1 | [Description] | [High/Med/Low] | [#Issue] | [Name] | [Date] |

## Next Period Focus
### Critical Path Tasks
- T##: [Task name] - [Critical dependency]

### GitHub Activities  
- [Expected issue comments/reviews]
- [Planned task imports/exports]

### Stakeholder Actions Required
- [Decision needed]
- [Approval required]
```

#### Automated Reporting Capabilities

When using GitHub integration:

1. **Task Progress**: Automatically calculated from T##-task-name.md file states
2. **Team Activity**: GitHub API provides collaboration metrics
3. **Dependency Tracking**: PERT analysis combined with GitHub issue linking
4. **Historical Trends**: GitHub commit history shows documentation evolution
5. **External Feedback**: Issue comments provide stakeholder input metrics

This approach enables both traditional project reporting and modern distributed team collaboration tracking.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zhongadamwang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

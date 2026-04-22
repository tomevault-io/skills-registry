---
name: course-template-support
description: Helps navigate and use the course development template structure. Use when creating new courses, setting up modules, organizing assets, or when user mentions "new course," "course setup," "template," "create module," or "organize course files. Use when this capability is needed.
metadata:
  author: webmasterarbez
---

# Course Template Support

Guide for using the course development template structure effectively.

## Quick Start Commands

### Create a New Course

```bash
# Copy template to new course
cp -r course-template/ my-new-course/

# Or with a specific name
cp -r course-template/ safety-training-2024/
```

### Create a New Module

```bash
# From within your course folder
cp -r 02-development/modules/module-template/ 02-development/modules/module-02/
```

## Template Structure Reference

```
course-[name]/
├── 00-analysis/                 # ADDIE Phase 1: Analyze
│   ├── needs-assessment.md      # Business case, performance gap
│   ├── learner-profile.md       # Target audience definition
│   └── constraints.md           # Budget, timeline, technical limits
│
├── 01-design/                   # ADDIE Phase 2: Design
│   ├── course-design-document.md    # Master planning document
│   ├── learning-objectives.md       # ABCD format objectives
│   ├── curriculum-outline.md        # Module structure
│   ├── assessment-strategy.md       # Assessment plan
│   └── storyboards/                 # Visual scripts per module
│
├── 02-development/              # ADDIE Phase 3: Develop
│   ├── modules/
│   │   ├── module-01/           # First module
│   │   │   ├── content/         # HTML, source files
│   │   │   ├── assets/          # Module-specific media
│   │   │   │   ├── images/
│   │   │   │   ├── video/
│   │   │   │   ├── audio/
│   │   │   │   └── documents/
│   │   │   └── assessments/     # Quiz files
│   │   └── module-template/     # Copy for new modules
│   └── shared-assets/           # Course-wide media
│       ├── images/
│       ├── video/
│       ├── audio/
│       ├── fonts/
│       └── branding/
│
├── 03-implementation/           # ADDIE Phase 4: Implement
│   ├── facilitator-guide.md
│   ├── learner-communications/
│   └── support-documentation/
│
├── 04-evaluation/               # ADDIE Phase 5: Evaluate
│   ├── evaluation-plan.md
│   ├── surveys/
│   └── reports/
│
├── delivery-formats/            # Format-specific materials
│   ├── self-paced/
│   ├── microlearning/
│   ├── blended/
│   └── vilt/
│
├── output/                      # LMS packages
│   ├── scorm-1.2/
│   ├── scorm-2004/
│   ├── xapi/
│   └── cmi5/
│
└── _templates/                  # Reusable templates
    ├── assessment-template.md
    └── storyboard-template.md
```

## Workflow Guide

### Phase 1: Analysis (Start Here)

Complete these documents before designing:

1. **needs-assessment.md**
   - What problem are we solving?
   - Who requested this training?
   - What does success look like?

2. **learner-profile.md**
   - Who are the learners?
   - What do they already know?
   - What barriers exist?

3. **constraints.md**
   - Budget and timeline
   - Technical limitations
   - Resource availability

### Phase 2: Design

Plan before building:

1. **course-design-document.md** (Start here)
   - Overall course plan
   - References other design docs

2. **learning-objectives.md**
   - Use ABCD format
   - One objective per skill

3. **curriculum-outline.md**
   - Module structure
   - Topic sequencing

4. **assessment-strategy.md**
   - Formative and summative
   - Alignment to objectives

5. **storyboards/** (Create one per module)
   - Copy `_templates/storyboard-template.md`
   - Visual script for development

### Phase 3: Development

Build your content:

1. **Create modules**
   ```bash
   cp -r modules/module-template/ modules/module-02/
   ```

2. **Add content** to `modules/module-XX/content/`

3. **Organize assets** by type:
   - `images/` - diagrams, screenshots, icons
   - `video/` - mp4, webm files
   - `audio/` - narration, sound effects
   - `documents/` - PDFs, handouts

4. **Create assessments** in `modules/module-XX/assessments/`

5. **Use shared-assets/** for course-wide resources

### Phase 4: Implementation

Prepare for delivery:

1. **facilitator-guide.md**
   - Facilitation instructions
   - Activity details
   - Troubleshooting

2. **learner-communications/**
   - Enrollment emails
   - Reminder messages
   - Completion notices

3. **support-documentation/**
   - FAQs
   - Help guides
   - Technical support info

### Phase 5: Evaluation

Measure and improve:

1. **evaluation-plan.md**
   - Kirkpatrick levels
   - Data collection methods
   - Success criteria

2. **surveys/**
   - Reaction surveys
   - Follow-up assessments

3. **reports/**
   - Analysis documents
   - Improvement recommendations

## Asset Naming Conventions

### Files

```
[type]-[description]-[variant].[ext]

Examples:
diagram-sales-process.png
diagram-sales-process-simplified.png
icon-checkmark-green.svg
photo-team-meeting-01.jpg
video-intro-welcome.mp4
audio-narration-screen-01.mp3
```

### Modules

```
module-[number]/

Examples:
module-01/
module-02/
module-03-optional/
```

## Common Tasks

### Add a Video to a Module

1. Place video file in `modules/module-XX/assets/video/`
2. Name it descriptively: `demo-software-login.mp4`
3. Reference in HTML: `<video src="../assets/video/demo-software-login.mp4">`

### Create a Knowledge Check

1. Copy `_templates/assessment-template.md`
2. Save to `modules/module-XX/assessments/quiz-knowledge-check.md`
3. Fill in questions aligned to objectives

### Add Shared Branding

1. Place logos in `shared-assets/branding/`
2. Place fonts in `shared-assets/fonts/`
3. Reference from modules: `../../shared-assets/branding/logo.svg`

### Export for LMS

1. Build your SCORM/xAPI package
2. Save to appropriate `output/` folder:
   - `output/scorm-1.2/course-name_v1.0_scorm12_2024-01-15.zip`
   - `output/scorm-2004/course-name_v1.0_scorm2004_2024-01-15.zip`

### Add a New Delivery Format

Use format-specific folders:

- **Self-paced**: Primary modules in `02-development/modules/`
- **Microlearning**: Short nuggets in `delivery-formats/microlearning/`
- **Blended**: Pre-work, live session, follow-up materials
- **VILT**: Session plans, participant materials

## File Checklist by Phase

### Analysis Complete
- [ ] needs-assessment.md filled out
- [ ] learner-profile.md filled out
- [ ] constraints.md filled out
- [ ] Stakeholder sign-off obtained

### Design Complete
- [ ] course-design-document.md approved
- [ ] learning-objectives.md finalized
- [ ] curriculum-outline.md complete
- [ ] assessment-strategy.md defined
- [ ] Storyboards created for each module

### Development Complete
- [ ] All modules built
- [ ] All assets in correct folders
- [ ] Assessments created and tested
- [ ] Accessibility checked
- [ ] Content reviewed by SME

### Implementation Ready
- [ ] facilitator-guide.md complete (if applicable)
- [ ] Learner communications drafted
- [ ] Support documentation ready
- [ ] LMS package tested

### Evaluation Ready
- [ ] evaluation-plan.md complete
- [ ] Surveys created
- [ ] Baseline data collected

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/webmasterarbez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

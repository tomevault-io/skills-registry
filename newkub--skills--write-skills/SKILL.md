---
name: write-skills
description: description: กำหนดมาตรฐานการสร้าง skill files สำหรับ Windsurf workflow system Use when this capability is needed.
metadata:
  author: newkub
---
---
title: Write Skills
description: กำหนดมาตรฐานการสร้าง skill files สำหรับ Windsurf workflow system
type: skill
version: 1.0.0
auto_execution_mode: 3
file-patterns: ["SKILL.md", "*.md"]
follow:
  skills: ["@write-workflows", "@write-markdown"]
  workflows: ["/write-workflows", "/validate"]
  files: []
  mcp: []
---

# Write Skills

## Purpose

สร้างมาตรฐานและโครงสร้างสำหรับการพัฒนา skill files ใน Windsurf workflow system เพื่อให้สอดคล้องกันและง่ายต่อการบำรุงรักษา

## When to Apply

ใช้สำหรับการสร้าง skill files ใหม่ทั้งหมด, การอัพเดทหรือปรับปรุง skills ที่มีอยู่, การตรวจสอบความสมบูรณ์ของ skill structure, และการสร้าง documentation สำหรับ skills

ไม่ใช้สำหรับการสร้าง workflow files (ใช้ @write-workflows แทน), การเขียน markdown ทั่วไป (ใช้ @write-markdown แทน), และการจัดการ project structure ทั่วไป

## Execute

### Phase 1: Setup Structure
1. สร้างโครงสร้างโฟลเดอร์ตามมาตรฐาน
2. สร้างไฟล์ `SKILL.md` หลัก
3. ตั้งค่า frontmatter ให้ถูกต้อง

### Phase 2: Content Creation
1. เขียนส่วน Purpose และ Scope
2. สร้าง Quick Reference table
3. อธิบาย Execute steps

### Phase 3: Documentation
1. สร้าง knowledge content
2. เพิ่ม reference materials
3. สร้าง examples และ templates

### Phase 4: Validation
1. ตรวจสอบความสมบูรณ์ของทุก sections
2. ตรวจสอบการเชื่อมโยงกับ skills/workflows อื่น
3. ทดสอบความสามารถในการ execute

## File Structure

```
skill-name/
├── SKILL.md                    # MUST: Main definition
├── 1-rules/                    # กฎและมาตรฐาน
│   ├── 1-directory-overview.md
│   ├── 1-must-directories.md
│   ├── 1-optional-directories.md
│   ├── 2-content-organization.md
│   ├── 3-file-naming.md
│   ├── 4-frontmatter-standards.md
│   ├── 5-follow-workflows.md
│   ├── 7-follow-markdown.md
│   └── 8-markdown-standards.md

├── 3-workflows/                # Workflows สำหรับ skill execution
├── 4-examples/                 # ตัวอย่างโปรเจกต์
├── 5-knowledge/                # แนวคิดหลัก
├── 6-reference/                # แหล่งอ้างอิง
├── 7-commands/                 # CLI commands (.ts + cac)
└── SKILL.md

```

## โครงสร้าง Directory

### 1-rules/ - กฎและมาตรฐาน

```
1-rules/
├── structure/                  # โครงสร้าง project และ architectures
│   ├── 1-structure-directory-overview.md
│   ├── 1-structure-tools.md
│   ├── 1-structure-write.md
│   ├── 1-structure-integration.md
│   ├── 1-structure-testing.md
│   ├── 1-structure-lib.md
│   ├── 1-structure-app.md
│   ├── 1-structure-monorepo.md
│   ├── 1-structure-microservice.md
│   └── 2-structure-optional-directories.md
├── patterns/                   # Design patterns และ best practices
│   └── 3-patterns-commands-standards.md
└── content/                    # เนื้อหาและมาตรฐานการเขียน
    ├── 3-content-content-organization.md
    ├── 4-content-file-naming.md
    ├── 5-content-frontmatter-standards.md
    ├── 6-content-follow-workflows.md
    ├── 7-content-follow-markdown.md
    └── 8-content-markdown-standards.md
```

## หมวดหมู่ไฟล์

### 1-rules/ - กฎและมาตรฐาน

| Title | Description |
|-------|-------------|
| 1-structure-directory-overview.md | ภาพรวมโครงสร้างและหลักการสำคัญ |
| 1-structure-tools.md | โครงสร้างสำหรับ CLI tools และ utilities |
| 1-structure-write.md | โครงสร้างสำหรับ writing systems และ content generation |
| 1-structure-integration.md | โครงสร้างสำหรับ system integration และ API connections |
| 1-structure-testing.md | โครงสร้างสำหรับ testing frameworks และ test organization |
| 1-structure-lib.md | โครงสร้างสำหรับ library code และ shared components |
| 1-structure-app.md | โครงสร้างสำหรับ full-stack application development |
| 1-structure-monorepo.md | โครงสร้างสำหรับ monorepo development |
| 1-structure-microservice.md | โครงสร้างสำหรับ microservices architecture |
| 2-structure-optional-directories.md | ไดเรกทอรีเสริมและวิธีการตัดสินใจ |
| 3-patterns-commands-standards.md | มาตรฐานการสร้าง CLI commands (.ts + cac) |
| 3-content-content-organization.md | การจัดระเบียบเนื้อหาใน skill files |
| 4-content-file-naming.md | กฎการตั้งชื่อไฟล์มาตรฐาน |
| 5-content-frontmatter-standards.md | มาตรฐาน YAML frontmatter |
| 6-content-follow-workflows.md | การเชื่อมโยงกับ workflows อื่น |
| 7-content-follow-markdown.md | การเชื่อมโยงกับ markdown skills |
| 8-content-markdown-standards.md | มาตรฐานการเขียน markdown |

### 2-templates/ - เทมเพลต

| Title | Description |
|-------|-------------|
| skill-template.md | เทมเพลตพื้นฐานสำหรับสร้าง skill ใหม่ |

### 3-workflows/ - Workflows สำหรับ skill execution

| Title | Description |
|-------|-------------|
| 1-basic-workflow.md | Workflow พื้นฐานสำหรับ skill execution |
| 2-intermediate-workflow.md | Workflow ระดับกลางพร้อม validation |
| 3-advanced-workflow.md | Workflow ขั้นสูงพร้อม error handling |

### 4-examples/ - ตัวอย่างโปรเจกต์

| Title | Description |
|-------|-------------|
| web-development-skill/ | Skill สำหรับพัฒนาเว็บ |
| cli-tool-skill/ | Skill สำหรับเครื่องมือ CLI |
| api-integration-skill/ | Skill สำหรับการเชื่อมต่อ API |

### 5-knowledge/ - แนวคิดหลัก

| Title | Description |
|-------|-------------|
| core-concepts.md | แนวคิดพื้นฐานของ skill system |
| best-practices.md | แนวทางปฏิบัติที่ดีที่สุด |

### 6-reference/ - แหล่งอ้างอิง

| Title | Description |
|-------|-------------|
| external-resources.md | แหล่งข้อมูลภายนอก |
| examples.md | ตัวอย่างเพิ่มเติม |

### 7-commands/ - CLI commands

| Title | Description |
|-------|-------------|
| build.ts | Command สำหรับ build project |
| dev.ts | Command สำหรับ development mode |
| test.ts | Command สำหรับ run tests |
| deploy.ts | Command สำหรับ deployment |
| lint.ts | Command สำหรับ linting และ formatting |
| clean.ts | Command สำหรับ cleaning artifacts |
| index.ts | Main entry point สำหรับ CLI |

### 8-patterns/ - Design patterns และ best practices

| Title | Description |
|-------|-------------|
| 1-skill-structure-patterns.md | Patterns สำหรับโครงสร้าง skills |
| 2-workflow-patterns.md | Patterns สำหรับการสร้าง workflows |
| 3-integration-patterns.md | Patterns สำหรับการเชื่อมโยงระบบ |
| 4-error-handling-patterns.md | Patterns สำหรับการจัดการ error |

### 9-structures/ - Project structures และ architectures

| Title | Description |
|-------|-------------|
| 1-tools-structure.md | โครงสร้างสำหรับ CLI tools และ utilities |
| 2-write-structure.md | โครงสร้างสำหรับ writing systems และ content generation |
| 3-integration-structure.md | โครงสร้างสำหรับ system integration และ API connections |
| 4-testing-structure.md | โครงสร้างสำหรับ testing frameworks และ test organization |

### Core Files

| Title | Description |
|-------|-------------|
| SKILL.md | ไฟล์หลักของ skill ที่มีข้อมูลทั้งหมด |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/newkub) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

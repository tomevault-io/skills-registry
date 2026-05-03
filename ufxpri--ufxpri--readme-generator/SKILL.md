---
name: readme-generator
description: Generate GitHub profile README.md from career data. Use when creating or updating GitHub profile page with auto-generated content from resume and career files. Use when this capability is needed.
metadata:
  author: ufxpri
---

# README Generator Skill

## Purpose
Create a **professional GitHub profile README.md** optimized for the **Korean job market**, focusing on concrete projects and essential hiring information.

## Task
Generate `README.md` (GitHub profile page) by synthesizing:
- `RESUME.md` - For extracting top projects
- `basic_info.md` - Static info (name, contact, education, military, certs)
- `what_i_did_*.md` - For extracting specific project names
- Current GitHub profile style (if existing README.md present)

## Instructions

### Step 1: Read Source Data

Read the following files (if they exist):
- `basic_info.md` - **PRIMARY SOURCE** for static info (name, contact, education, military, certs, career)
- `RESUME.md` - For extracting top projects and achievements
- `what_i_did_*.md` - For specific project names and details
- Existing `README.md` - To preserve user's preferred style/badges

### Step 2: Extract Key Information

From the source files, identify:
- **Name and title** (e.g., "조승준 | Backend Developer")
- **Current role and company**
- **Tech stack** (primary languages, frameworks, tools)
- **Top 3-5 highlights** (biggest achievements with metrics)
- **Certifications and credentials**
- **Community involvement** (meetups, conferences, open source)
- **Contact/links** (email, LinkedIn, blog, etc.)

### Step 3: Design README Structure (Korean Job Market)

**CRITICAL**: For Korean job market, README should be:
- **한글로 작성** (Write entirely in Korean)
- **구체적인 프로젝트 중심** (Focus on concrete project names, not vague metrics)
- **채용 필수 정보 포함** (Include education and military service - mandatory for Korean hiring)

#### Required Structure (Following shields.io badge format):

**1. Header**
```markdown
# [이름] [Github Handle]

### 👥 [Role/Title]
```
- Format: `# [Name] [Handle]` (no parentheses)
- Title: `### 👥 [Role]` (use ### for subheader)

**2. 기술 스택 (Tech Stack) - Badge Format**
```markdown
![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54)
![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=for-the-badge&logo=fastapi)
```
- **CRITICAL**: Use `style=for-the-badge` (NOT flat-square)
- Copy badges directly from `basic_info.md` 기술 스택 section
- Core technologies only (6-10 badges max)
- All badges on separate lines (markdown will display inline)

**3. Links Section**
```markdown
more info [COVER LETTER](./COVER_LETTER.md) | [RESUME](./RESUME.md)
```
- Simple text with pipe separator
- Links to cover letter and resume

**4. 경력 (Career) - Compact Format with sup/sub**
```markdown
**[회사1] @[handle1]** <sup><sub>[직무1] ([시작일] ~ [종료일])</sub></sup>
**[회사2] @[handle2]** <sup><sub>[직무2] ([시작일] ~ 현재)</sub></sup>
```
- Format: `**Company @handle** <sup><sub>role (dates)</sub></sup>`
- Add two spaces at end of first line for line break
- No bullet points or sections

**5. 커뮤니티 활동 (Community) - Same Compact Format**
```markdown
**[커뮤니티1]** <sup><sub>[역할1] ([시작일] ~)</sub></sup>
**[커뮤니티2]** <sup><sub>[역할2] ([시작일] ~ [종료일])</sub></sup>
```
- Same format as career section
- Add two spaces at end of first line for line break

**6. Horizontal Line**
```markdown
---
```

**7. 자격증 (Certifications) - Inline Format**
```markdown
**[자격증1]** <sup><sub>([취득일1])</sub></sup> [badge](url)
**[자격증2]** <sup><sub>([취득일2])</sub></sup> [badge](url)
**[자격증3]** <sup><sub>([취득일3])</sub></sup> [인증번호]
```
- Format: `**Cert Name** <sup><sub>(date)</sub></sup> [badge](url)` or cert number
- Add two spaces at end of each line for line breaks
- Copy from `basic_info.md` 자격증 section

### Step 4: Generate Content with LLM Intelligence

Use your language model capabilities to:
- **Extract essence**: What's most impressive? Lead with that.
- **Be concise**: Profile README should be scannable, not exhaustive
- **Show personality**: Balance professional with approachable
- **Use visuals**: Badges, stats, emojis (sparingly)
- **Link to details**: Point to full RESUME.md, projects, etc.

### Step 5: Preserve User Style

If existing README.md has:
- Specific badge style → Keep it
- Preferred layout → Maintain it
- Custom sections → Preserve them
- Emoji usage → Match the tone

Update content, don't replace style.

### Step 6: Write Output

Write to `README.md` in the base directory.

## Example Output Structure (Korean Market Standard)

```markdown
# [이름] [Github Handle]

### 👥 [Role/Title]

![Tech1](https://img.shields.io/badge/tech1-color?style=for-the-badge&logo=tech1&logoColor=white)
![Tech2](https://img.shields.io/badge/Tech2-color?style=for-the-badge&logo=tech2)
![Tech3](https://img.shields.io/badge/Tech3-color?style=for-the-badge&logo=tech3&logoColor=white)
![Tech4](https://img.shields.io/badge/Tech4-color?style=for-the-badge&logo=tech4&logoColor=white)
![Tech5](https://img.shields.io/badge/tech5-color?style=for-the-badge&logo=tech5&logoColor=white)
![Tech6](https://img.shields.io/badge/Tech6-color?style=for-the-badge&logo=tech6&logoColor=white)

more info [COVER LETTER](./COVER_LETTER.md) | [RESUME](./RESUME.md)

**[회사1] @[handle1]** <sup><sub>[직무1] ([시작일] ~ [종료일])</sub></sup>
**[회사2] @[handle2]** <sup><sub>[직무2] ([시작일] ~ 현재)</sub></sup>

**[커뮤니티1]** <sup><sub>[역할1] ([시작일] ~)</sub></sup>
**[커뮤니티2]** <sup><sub>[역할2] ([시작일] ~ [종료일])</sub></sup>

---

**[자격증1]** <sup><sub>([취득일])</sub></sup> [badge](url)
**[자격증2]** <sup><sub>([취득일])</sub></sup> [badge](url)
**[자격증3]** <sup><sub>([취득일])</sub></sup> [인증번호]
```

## Formatting Rules (shields.io badge style)

**CRITICAL**: Follow this exact format from the guide:

### Header Format:
```markdown
# [이름] [Handle]

### 👥 [Role/Title]
```
- Use `#` for name (no parentheses)
- Use `###` for title/role

### Badge Format:
```markdown
![Tech1](https://img.shields.io/badge/tech1-color?style=for-the-badge&logo=tech1&logoColor=color)
![Tech2](https://img.shields.io/badge/Tech2-color?style=for-the-badge&logo=tech2)
```
- **MUST use `style=for-the-badge`** (NOT flat-square)
- Copy badges directly from `basic_info.md` 기술 스택 section
- Each badge on separate line (will display inline)

### Compact Format with sup/sub:
```markdown
**[회사1] @[handle1]** <sup><sub>[직무1] ([시작일] ~ [종료일])</sub></sup>
**[회사2] @[handle2]** <sup><sub>[직무2] ([시작일] ~ 현재)</sub></sup>

**[커뮤니티1]** <sup><sub>[역할1] ([시작일] ~)</sub></sup>
**[커뮤니티2]** <sup><sub>[역할2] ([시작일] ~ [종료일])</sub></sup>
```
- Format: `**Company @handle** <sup><sub>role (dates)</sub></sup>`
- **Two spaces at end of line** for line break
- **Blank line** between career and community sections
- No section headers, no bullet points

### Links Format:
```markdown
more info [COVER LETTER](./COVER_LETTER.md) | [RESUME](./RESUME.md)
```
- Simple text with pipe separator

### Certifications Format:
```markdown
---

**[자격증1]** <sup><sub>([취득일])</sub></sup> [badge](url)
**[자격증2]** <sup><sub>([취득일])</sub></sup> [badge](url)
**[자격증3]** <sup><sub>([취득일])</sub></sup> [인증번호]
```
- Horizontal line `---` before certifications
- Format: `**Cert** <sup><sub>(date)</sub></sup> [badge](url)`
- Two spaces at end of each line

## Content Guidelines (Korean Job Market)

### DO:
- ✅ **한글로 작성** - Write everything in Korean (except tech terms)
- ✅ **구체적인 프로젝트명 사용** - Use specific project names (client + system)
  - Good: "[고객사] [시스템명] 프로젝트"
  - Bad: "AI monitoring system" or "CPU optimization"
- ✅ **학력/병역 필수 포함** - Always include education and military service
- ✅ **간결하게** - Keep it scannable (3-4 top projects, not 10)
- ✅ **핵심만** - Details go to RESUME.md, only highlights in README
- ✅ **기술 스택 정확히** - Only list technologies actually used (8-10 badges max)

### DON'T:
- ❌ **영어로 작성하지 말것** - Don't write in English (this is for Korean hiring managers)
- ❌ **추상적인 성과** - Don't use vague achievements without project context
  - Bad: "Reduced CPU by 50%" (뭘 했는지 모름)
  - Good: "[고객사] 시스템에서 [기술] 최적화로 [메트릭] [X]% 절감"
- ❌ **학력/병역 빠뜨리지 말것** - Never omit education/military (Korean recruiters always check)
- ❌ **너무 길게** - Don't make it too long (aim for 1 screen, 2 max)
- ❌ **모든 프로젝트 나열** - Don't list every project (only top 3-4)

## Customization Options

You may receive instructions like:
- "Focus on AI/ML work" → Emphasize ML projects, models, data pipelines
- "Highlight open source" → Feature OSS contributions prominently
- "More visual" → Add more badges, charts, diagrams
- "Minimal style" → Simpler layout, fewer emojis
- "Include recent blog posts" → Add section for latest writing

Adapt accordingly.

## Auto-Update Strategy

When resume data changes:
1. User runs: "Update my GitHub profile README"
2. This skill reads latest RESUME.md and career data
3. Regenerates README.md preserving style
4. User commits and pushes to GitHub

Optional: Set up GitHub Action to auto-generate README on push to career data.

## Success Criteria
- README.md accurately represents current career state
- Most impressive achievements highlighted
- Easy to scan and read
- Links to detailed content work
- Badges and stats display correctly
- Professional yet approachable tone

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ufxpri) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

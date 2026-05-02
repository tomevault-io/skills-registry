---
name: create-github-readme
description: Generate a professional GitHub README.md file following open standards with tech badges, architecture diagrams, setup instructions, and acknowledgements. Use when this capability is needed.
metadata:
  author: alfredang
---

# Create GitHub README

## Command
`/create_github_readme` or `create_github_readme`

## Navigate
Documentation

## Keywords
readme, github readme, create readme, generate readme, project readme, documentation, readme.md, update readme, write readme, project documentation

## Description
Generate a professional GitHub README.md file for the WSQ Courseware Generator project following open standards and best practices.

## Execution
This skill runs using **Claude Code with subscription plan**. Do NOT use pay-as-you-go API keys. All AI operations should be executed through the Claude Code CLI environment with an active subscription.

## Response
I'll generate a comprehensive GitHub README.md for the project.

The README will include:

| Section | Description |
|---------|-------------|
| **Header** | Project name, badges, tagline, links |
| **About** | Project description and key features |
| **Tech Stack** | Technologies used with badges |
| **Architecture** | System diagram (ASCII art) |
| **Project Structure** | File/folder organization |
| **Getting Started** | Prerequisites, installation, running |
| **Deployment** | Docker, Hugging Face Spaces instructions |
| **Contributing** | Fork, PR workflow, discussions |
| **Developed By** | Tertiary Infotech Academy Pte. Ltd. |
| **Acknowledgements** | Credits and thanks |

## Instructions
When generating a README, follow this open standard structure:

### 1. Header Section
```markdown
<div align="center">

# Project Name

[![Badge1](url)](link)
[![Badge2](url)](link)

**Tagline**

[Demo](url) · [Report Bug](url) · [Request Feature](url)

</div>
```

### 2. Required Badges
- Python version
- Framework (Chainlit)
- AI Provider (Claude/Anthropic)
- License
- Docker
- Deployment platform

### 3. About Section
- Brief description (2-3 sentences)
- Key features as bullet points or table
- Platform statistics (optional)

### 4. Tech Stack
Table format with categories:
- Frontend
- Backend
- AI/LLM
- Database
- Deployment
- Document Processing

### 5. Architecture Diagram
ASCII art showing:
- UI layer
- Application layer
- AI agents
- Data/document layer

### 6. Project Structure
```
project/
├── app.py
├── modules/
└── ...
```

### 7. Getting Started
- Prerequisites
- Clone & install steps
- Environment setup
- Run command

### 8. Deployment
- Docker instructions
- Cloud platform instructions

### 9. Contributing
- Fork instructions
- PR workflow
- Link to discussions

### 10. Footer
- Developed by: Company name
- Acknowledgements: Credits
- Call to action: Star the repo

## Capabilities
- Generate complete README.md following GitHub best practices
- Include technology badges with shields.io
- Create ASCII architecture diagrams
- Document file structure
- Provide multi-platform deployment instructions
- Add proper attribution and acknowledgements

## Next Steps
After generating the README:
1. Review and customize content for your project
2. Update placeholder URLs and usernames
3. Add screenshots if available
4. Verify all links work
5. Commit to repository

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alfredang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

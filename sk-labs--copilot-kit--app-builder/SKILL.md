---
name: app-builder
description: Main application building orchestrator. Creates full-stack applications from natural language requests. Determines project type, selects tech stack, coordinates agents. Use when this capability is needed.
metadata:
  author: sk-labs
---

# App Builder - Application Building Orchestrator

> Analyzes user's requests, determines tech stack, plans structure, and coordinates agents.

## 🎯 Selective Reading Rule

**Read ONLY files relevant to the request!** Check the content map, find what you need.

| File | Description | When to Read |
|------|-------------|--------------|
| `project-detection.md` | Keyword matrix, project type detection | Starting new project |
| `tech-stack.md` | 2026 default stack, alternatives | Choosing technologies |
| `agent-coordination.md` | Agent pipeline, execution order | Coordinating multi-agent work |
| `scaffolding.md` | Directory structure, core files | Creating project structure |
| `feature-building.md` | Feature analysis, error handling | Adding features to existing project |
| `templates/SKILL.md` | **Project templates** | Scaffolding new project |

---

## 📦 Supported Project Types
 
 The App Builder supports the following architectures. Use these as reference when planning structure:
 
 | Type | Tech Stack | Description |
 |------|------------|-------------|
 | **Next.js Fullstack** | Next.js + Prisma | Full-stack web app with database |
 | **Next.js SaaS** | Next.js + Stripe | SaaS product skeleton |
 | **Next.js Static** | Next.js + Framer | High-performance landing page |
 | **Nuxt App** | Nuxt 3 + Pinia | Vue full-stack application |
 | **Express API** | Express + JWT | RESTful API service |
 | **Python FastAPI** | FastAPI | High-performance Python API |
 | **React Native** | Expo + Zustand | Mobile application (iOS/Android) |
 | **Flutter App** | Flutter + Riverpod | Cross-platform mobile app |
 | **Electron Desktop** | Electron + React | Cross-platform desktop app |
 | **Chrome Extension** | Chrome MV3 | Browser extension manifest v3 |
 | **CLI Tool** | Node.js + Commander | Command-line interface tool |
 | **Monorepo** | Turborepo + pnpm | Multi-package workspace |

---

## 🔗 Related Agents

| Agent | Role |
|-------|------|
| `project-planner` | Task breakdown, dependency graph |
| `frontend-specialist` | UI components, pages |
| `backend-specialist` | API, business logic |
| `database-architect` | Schema, migrations |
| `devops-engineer` | Deployment, preview |

---

## Usage Example

```
User: "Make an Instagram clone with photo sharing and likes"

App Builder Process:
1. Project type: Social Media App
2. Tech stack: Next.js + Prisma + Cloudinary + Clerk
3. Create plan:
   ├─ Database schema (users, posts, likes, follows)
   ├─ API routes (12 endpoints)
   ├─ Pages (feed, profile, upload)
   └─ Components (PostCard, Feed, LikeButton)
4. Coordinate agents
5. Report progress
6. Start preview
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sk-labs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

---
name: cva-overview
description: Overview of Clojure + Google ADK + Vertex AI development environment. Comprehensive lab for building production AI agents using Clojure as primary language, integrating Google ADK via Java SDK and Python libraries via libpython-clj. Includes healthcare pipeline with validated ROI (-99.4% time, -92.4% cost). Use when starting new projects, understanding architecture, or needing general context about the stack. Use when this capability is needed.
metadata:
  author: joaopelegrino
---

# 📚 Clojure + Google ADK + Vertex AI Laboratory

> **Version:** 1.2.0
> **Last Updated:** 2025-10-27
> **Objective:** Complete knowledge base for developing production AI agents using Clojure, Google ADK, and Vertex AI

---

## 🎯 Laboratory Vision

This laboratory explores creating AI agent solutions using **Clojure** as the primary language, integrating:

- **Google ADK (Agent Development Kit)** via Java SDK (native JVM)
- **Python libraries** via libpython-clj (NumPy, HuggingFace, etc.)
- **Vertex AI Agent Engine** for deployment
- **Functional programming** for agent orchestration

---

## 🏗️ Technology Stack

| Technology | Version | Purpose |
|-----------|---------|---------|
| **Clojure** | 1.11+ | Primary language |
| **Java** | 17+ | Runtime (JVM) and ADK SDK |
| **Python** | 3.10+ | Interop for ML/AI libraries |
| **Google ADK** | Latest | Agent framework |
| **libpython-clj** | 2.x | Python interop |
| **Vertex AI** | - | Deployment platform |

---

## 📖 Key Concepts

### Agent Types (A/B/C/D Taxonomy)

This lab uses a **validated taxonomy** of agent types based on capabilities:

- **Type A**: Pure AI (input → LLM → output) - ~$0.02, ~3s
- **Type B**: AI + CAG (Context-Aware Generation with database) - ~$0.08, ~5s
- **Type C**: AI + Web (Grounding with external APIs) - ~$0.18, ~12s
- **Type D**: AI + CAG + Web (maximum context) - ~$0.42, ~17s

> 📘 **Learn more:** See [`cva-concepts-agent-types`](../cva-concepts-agent-types/SKILL.md) skill for detailed explanation and decision tree.

### Multi-Model Strategy

Optimize costs by routing tasks to appropriate models:

- **Gemini Flash** (70%): Simple tasks, extraction, classification
- **Claude Haiku** (20%): Medium complexity, personalization
- **Claude Sonnet** (10%): Complex reasoning, consolidation

**Result:** 41% cost reduction vs Claude-only approach

---

## 🏥 Healthcare Pipeline (Production-Ready)

Complete 5-system pipeline for regulated medical content generation:

1. **S.1.1** (Type B): LGPD-compliant data extraction
2. **S.1.2** (Type A): Medical claims identification
3. **S.2-1.2** (Type C): Scientific reference search (PubMed, Scholar)
4. **S.3-2** (Type B): SEO optimization with professional profile
5. **S.4** (Type D): Final consolidation with compliance

### Validated ROI

**Real case:** Clínica Mente Saudável (20 posts/month)

- ⏱️ **Time:** 4h 15min → 1.5min (-99.4%)
- 💰 **Cost:** R$ 192.50 → R$ 14.70 (-92.4%)
- 📈 **ROI:** -R$ 3,850 → +R$ 3,094 (+180%)

> 📘 **Learn more:** See [`cva-healthcare-pipeline`](../cva-healthcare-pipeline/SKILL.md) skill for complete implementation.

---

## 🚀 Quick Start Path

### For Beginners (Clojure + ADK)

1. **Setup** → See [`cva-setup-vertex`](../cva-setup-vertex/SKILL.md) (⭐ START HERE)
2. **Concepts** → See [`cva-concepts-adk`](../cva-concepts-adk/SKILL.md)
3. **First Agent** → Use `/cva:new-agent` command
4. **Deploy** → Use `/cva:deploy` command

### For Experienced Clojure Developers

1. **ADK Overview** → See [`cva-concepts-adk`](../cva-concepts-adk/SKILL.md)
2. **Agent Types** → See [`cva-concepts-agent-types`](../cva-concepts-agent-types/SKILL.md)
3. **Quick Reference** → See [`cva-quickref-adk`](../cva-quickref-adk/SKILL.md)
4. **Advanced Patterns** → See [`cva-patterns-workflows`](../cva-patterns-workflows/SKILL.md)

### For Production Healthcare Systems

1. **GCP Context** → See [`cva-setup-vertex`](../cva-setup-vertex/SKILL.md) (credentials, costs)
2. **Agent Types** → See [`cva-concepts-agent-types`](../cva-concepts-agent-types/SKILL.md) (understand A/B/C/D)
3. **Compliance** → See [`cva-healthcare-compliance`](../cva-healthcare-compliance/SKILL.md) (LGPD, CFM, CRP)
4. **Pipeline** → See [`cva-healthcare-pipeline`](../cva-healthcare-pipeline/SKILL.md) (5-system workflow)
5. **Cost Optimization** → See [`cva-patterns-cost`](../cva-patterns-cost/SKILL.md) (multi-model routing)

---

## 📋 Initial Setup Checklist

- [ ] Clojure installed (1.11+)
- [ ] Java 17+ installed
- [ ] Python 3.10+ installed
- [ ] Google Cloud SDK configured
- [ ] Vertex AI API enabled
- [ ] Clojure project created with deps.edn
- [ ] libpython-clj configured and tested
- [ ] Google ADK Java SDK added to project
- [ ] Google Cloud credentials configured

> 📘 **Detailed instructions:** See [`cva-setup-clojure`](../cva-setup-clojure/SKILL.md), [`cva-setup-interop`](../cva-setup-interop/SKILL.md), and [`cva-setup-vertex`](../cva-setup-vertex/SKILL.md) skills.

---

## 🎯 Lab Objectives

1. **Explore** Clojure capabilities for AI agent development
2. **Integrate** Google ADK via Java SDK idiomatically
3. **Leverage** Python libraries (HuggingFace, NumPy) via libpython-clj
4. **Develop** architecture patterns for agents in Clojure
5. **Deploy** agents to Vertex AI Agent Engine
6. **Document** learnings and best practices

---

## 📊 Lab Status

- ✅ **Initial setup:** Complete
- ✅ **GCP/Vertex context:** Aggregated (project saas3-476116)
- ✅ **Validated credentials:** Complete
- ✅ **Base documentation:** Complete
- ✅ **Python ADK lessons:** Documented
- ✅ **Healthcare pipeline knowledge:** Aggregated (validated ROI)
- ✅ **Domain knowledge:** Healthcare, multi-model strategies
- ✅ **Advanced patterns:** Workflows, contexts, optimization
- 📋 **Production deployment:** Planned

---

## 🔗 Related Skills

### Setup & Configuration
- [`cva-setup-clojure`](../cva-setup-clojure/SKILL.md) - Clojure project setup
- [`cva-setup-interop`](../cva-setup-interop/SKILL.md) - libpython-clj configuration
- [`cva-setup-vertex`](../cva-setup-vertex/SKILL.md) - Vertex AI & GCP setup ⭐

### Core Concepts
- [`cva-concepts-adk`](../cva-concepts-adk/SKILL.md) - Google ADK architecture
- [`cva-concepts-agent-types`](../cva-concepts-agent-types/SKILL.md) - A/B/C/D taxonomy ⭐

### Quick References
- [`cva-quickref-adk`](../cva-quickref-adk/SKILL.md) - ADK API cheatsheet
- [`cva-quickref-libpython`](../cva-quickref-libpython/SKILL.md) - libpython-clj patterns

### Patterns & Best Practices
- [`cva-patterns-workflows`](../cva-patterns-workflows/SKILL.md) - Multi-agent workflows
- [`cva-patterns-context`](../cva-patterns-context/SKILL.md) - Context management (CAG)
- [`cva-patterns-cost`](../cva-patterns-cost/SKILL.md) - Cost optimization ⭐

### Healthcare Specialization
- [`cva-healthcare-pipeline`](../cva-healthcare-pipeline/SKILL.md) - Complete 5-system pipeline ⭐
- [`cva-healthcare-compliance`](../cva-healthcare-compliance/SKILL.md) - Brazilian regulations (LGPD, CFM, CRP)
- [`cva-healthcare-seo`](../cva-healthcare-seo/SKILL.md) - Medical SEO strategies

### Case Studies
- [`cva-case-study-roi`](../cva-case-study-roi/SKILL.md) - Validated ROI analysis ⭐

---

## 🛠️ Available Commands

Use these slash commands for productive workflows:

- `/cva:new-agent [type]` - Create new agent scaffold (A/B/C/D)
- `/cva:healthcare-workflow` - Generate complete healthcare pipeline
- `/cva:deploy [target]` - Deploy to Vertex AI or Cloud Run
- `/cva:cost-analysis` - Analyze workflow costs and suggest optimizations

---

## 🎓 Learning Resources

### Official Documentation
- [Clojure](https://clojure.org/)
- [Google ADK](https://google.github.io/adk-docs/)
- [Vertex AI](https://cloud.google.com/vertex-ai/docs)
- [libpython-clj](https://github.com/clj-python/libpython-clj)

### Community
- [ClojureVerse](https://clojureverse.org/)
- [Clojurians Slack](https://clojurians.slack.com/)
- [Clojurians Slack #data-science](https://clojurians.slack.com/archives/C0BQDEJ8M)

---

## 💡 Key Insights

★ **Functional Programming + AI Agents**: Clojure's immutability and REPL-driven development are excellent for agent orchestration and testing.

★ **JVM Native Advantage**: Using Google ADK Java SDK directly (no Python wrapper) provides better performance and type safety.

★ **Cost Optimization Matters**: Multi-model strategy (Gemini Flash 70%, Claude 20%, Sonnet 10%) reduces costs by 41% vs single-model approach.

★ **Type System for Agents**: The A/B/C/D taxonomy based on capabilities (not implementation) enables systematic architecture decisions and cost optimization.

★ **Healthcare ROI Validated**: -99.4% time and -92.4% cost reduction proven in production with Clínica Mente Saudável case study.

---

*This skill provides high-level context. Activate related skills for detailed implementation guidance.*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joaopelegrino) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

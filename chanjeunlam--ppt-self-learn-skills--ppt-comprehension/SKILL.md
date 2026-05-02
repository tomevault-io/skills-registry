---
name: ppt-comprehension
description: Expert-level PPTX analysis and conversion. Transforms presentations into deep, lecturer-quality study guides (Markdown/PDF). Features domain-aware explanations, strict bilingual terminology (Chinese/English), and 'One Slide, One Section' fidelity. Best for: understanding complex decks, creating study materials, and cross-language learning. Use when this capability is needed.
metadata:
  author: chanjeunlam
---

# PPT Comprehension Expert (Pro Max)

## 🌟 Overview(核心理念)

This is not just a converter; it is an **AI University Lecturer**.
Your goal is to transform a static PowerPoint presentation into a **Dynamic Study Guide**.
You do not just "translate text"; you **teach concepts**.

**Core Capabilities:**
1.  **Fidelity**: Preserves 100% of original images and structure.
2.  **Depth**: Explanations go beyond the slide text, providing context, logic, and significance.
3.  **Bilingual Precision**: Explanations are in Chinese, but **ALL** technical terms are anchored in English.

---

## 🛠 Prerequisites

Ensure the environment has the following tools (managed by `uv`):

*   **Core Logic**: `python`
*   **PPTX Handling**: `python-pptx`
*   **PDF Generation**: `Markdown`, `weasyprint`
*   **System Tools**: `libreoffice` (PPT->PDF), `poppler-utils` (PDF->Images)

---

## 🚀 Workflow (The 4-Step Protocol)

### 1. Extraction (Mechanical)
**Goal**: converting the proprietary `.pptx` binary into a readable `draft.md` with extracted images.

```bash
uv run --with python-pptx .agent/skills/ppt-comprehension/scripts/process_ppt.py <input.pptx> <output_dir>
```

**Output**:
-   `output/draft.md`: Contains text, image links, and **Unique Placeholders** (e.g., `> [AI: Explain Slide 5...]`).
-   `output/images/`: Extracted high-quality images.

### 2. Preparation (Safety)
**Goal**: Create the working file while preserving the mechanical fidelity of the draft.
**CRITICAL**: DO NOT write the study guide from scratch. You will lose formatting!

```bash
# Duplicate draft to safeguard original formatting
cp <output_dir>/draft.md <output_dir>/study_guide.md
```

### 3. Comprehension (The "Lecturer" Phase)
**Goal**: Visit every slide placeholder in `study_guide.md` and replace it with a **Lecturer-Level Explanation**.

**Command**: Use `multi_replace_file_content` to target strictly standard placeholders.

#### 🧠 The "Lecturer" Persona Rules
When filling the `> [AI: Explain Slide X...]` placeholder, follow these rules strictly:

1.  **Identify Domain**: First, determine the field (e.g., Biology, CS, History, Finance). Adjust your tone.
    *   *Biology/Science*: Focus on mechanisms, evolution, and data.
    *   *CS/Eng*: Focus on architecture, logic, and trade-offs.
    *   *Business*: Focus on strategy, market impact, and value.

2.  **Strict Bilingualism (Term-Anchoring)**:
    *   **Rule**: The first time a technical concept appears, it MUST be in the format: **Chinese Term (English Term)**.
    *   *Example*: "**微服务架构 (Microservices Architecture)** 允许我们独立扩展组件..."
    *   *Bad*: "微服务允许..." (Missing original term).

3.  **Expansion (The "Why")**:
    *   Slide text is often bullet points. Your job is to connect them.
    *   If a slide says "Cope's Law", you explain: "What is it? Why does it matter? Is it always true?"

4.  **Visual Processing**:
    *   If the slide has an image, **YOU MUST REFERENCE IT**.
    *   "As seen in the chart regarding **morphospace analysis**..."
    *   "The fossil image on the left demonstrates..."

### 4. Production (PDF Generation)
**Goal**: Render the final Markdown into a professional, shareable PDF.

```bash
uv run --with Markdown --with weasyprint .agent/skills/ppt-comprehension/scripts/md2pdf.py <output_dir>/study_guide.md <output_dir>/study_guide.pdf
```

---

## 📚 Example Scenarios

### Scenario A: Scientific/Biology Slide
**Slide Content**:
-   Ammonite Sutures
-   Goniatitic -> Ceratitic -> Ammonitic
-   Surface area increase?

**AI Explanation (Lecturer Output)**:
```markdown
**讲师讲解**：

本页展示了 **菊石 (Ammonites)** 演化中一个关键的解剖学特征：**缝合线 (Sutures)** 的复杂化。

*   **演化趋势 (Evolutionary Trend)**：
    *   我们可以清晰看到从早期的 **棱菊石 (Goniatitic)** 这种简单的折线，演化到 **齿菊石 (Ceratitic)** 的锯齿状，最后到 **真菊石 (Ammonitic)** 极其复杂的树枝状纹路。
*   **功能意义 (Functional Significance)**：
    *   这种复杂化极大地增加了 **壳体隔壁 (Septa)** 与外壳的接触面积。
    *   这增强了壳体对抗水压的结构强度，允许它们潜入更深的海域 (Deeper waters)。
```

### Scenario B: Technical/CS Slide
**Slide Content**:
-   Kubernetes Pods
-   Shared Network Namespace
-   Sidecar containers

**AI Explanation (Lecturer Output)**:
```markdown
**讲师讲解**：

这里我们进入 **Kubernetes** 的最小调度单元：**Pod**。

*   **核心概念 (Core Concept)**：
    *   Pod 不是单个容器，而是一个逻辑宿主。它其中的所有容器共享同一个 **网络命名空间 (Network Namespace)** 和 IP 地址。这意味着它们可以通过 `localhost` 互相通信。
*   **设计模式 (Design Pattern)**：
    *   注意图中的 "Helper"，这通常指 **边车模式 (Sidecar Pattern)**。
    *   辅助容器（如日志代理）与主业务容器部署在同一个 Pod 中，共享生命周期，但职责分离。
```

---

## ⚠️ Troubleshooting & Edge Cases

| Situation | Strategy |
| :--- | :--- |
| **Empty Slide (Images Only)** | "This slide relies entirely on visuals. Based on the previous context and the image content (e.g., a diagram of X), this likely represents..." |
| **Wall of Text** | Do not just copy it. **Synthesize** it. Break it down into 3 key takeaways using bullet points. |
| **Dense Data Table** | Do not read every number. Summarize the **Trend (趋势)**. "The data clearly shows a positive correlation between X and Y." |
| **Unknown Terminology** | Use your internal knowledge base to infer meaning from context, but mark it as inferred. |

---

## ✅ Best Practices Checklist

-   [ ] **One Section Per Slide**: Never merge slides.
-   [ ] **Bilingual Terms**: Are key English terms present?
-   [ ] **Image References**: Did I mention the visuals?
-   [ ] **Formatting**: Used bolding for emphasis, bullets for lists.
-   [ ] **Structure**: Used the "Copy & Edit" workflow to preserve original layout.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chanjeunlam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

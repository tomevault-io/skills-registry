---
name: mcp-builder
description: Guide for creating high-quality MCP (Model Context Protocol) servers using FastMCP (Python). Focuses on modern patterns, performance, and advanced features. Use when this capability is needed.
metadata:
  author: fzozyurt
---

# MCP Server Development Guide

## Overview

Create MCP (Model Context Protocol) servers that enable LLMs to interact with external services through well-designed tools. The quality of an MCP server is measured by how well it enables LLMs to accomplish real-world tasks. This guide prioritizes **FastMCP** for Python development due to its performance, developer experience, and advanced features.

> **Source of Truth**: For all FastMCP details, always refer to `https://gofastmcp.com/llms.txt`. This file contains the complete, up-to-date documentation index.

---

# Core Concepts: The MCP Primitives

Understanding these primitives is crucial for designing effective servers:

### 1. Tools (Action & Execution)
*   **What**: Executable functions that take arguments and return data or perform side effects.
*   **Use Case**: APIs, calculations, database writes, complex queries.
*   **Thinking**: "I need the LLM to *do* something."

### 2. Resources (Context Loading)
*   **What**: Data sources that can be read like files. They are identified by URIs (e.g., `file://...`, `postgres://...`).
*   **Use Case**: Reading logs, fetching configuration, viewing database rows, reading file contents.
*   **Thinking**: "I need to give the LLM *context* to read before it acts."

### 3. Prompts (Steering & Templating)
*   **What**: Pre-defined templates that guide the LLM's behavior or set up a specific task.
*   **Use Case**: "Code Review", "Bug Fix", "Summarize Logs", "Generate SQL".
*   **Thinking**: "I want to help the user *start* a common workflow with best practices."
*   **Importance**: Prompt Engineering is critical. A well-structured prompt ensures the LLM knows *how* to use the tools effectively.

---

# Process

## 🚀 High-Level Workflow

### Phase 1: Deep Research and Planning

#### 1.1 Understand Modern MCP Design
*   **API Coverage**: Balance raw API endpoints with helpful workflow tools.
*   **Context**: Use Resources to let agents "look before they leap."
*   **Feedback**: Use `ctx.info()` and `ctx.report_progress()` to keep the user informed during long tasks.

#### 1.2 Study Framework Documentation

**Recommended Stack: Python (FastMCP)**
FastMCP is the preferred framework. It handles the protocol details allowing you to focus on logic.

*   **Core Guide**: [🐍 FastMCP Core Guide](./reference/fastmcp_server.md)
    *   Setup, Tools, Resources, Prompts
    *   Context, Logging, Progress
    *   Authentication & User Elicitation
*   **Advanced Architecture**: [🏗️ FastMCP Advanced](./reference/fastmcp_advanced_architecture.md)
    *   FastAPI Integration
    *   OpenTelemetry (Metrics/Tracing)
    *   Namespaces & Transforms (Prompts/Resources as Tools)
    *   Visibility Control

#### 1.3 Plan Your Implementation
1.  **Map Primitives**: Which API endpoints become Tools? Which become Resources?
2.  **Define Prompts**: What represent the top 3-5 user workflows?

---

### Phase 2: Implementation

#### 2.1 Set Up Project Structure
Use `uv` for modern dependency management.
```bash
uv init my-server
uv add fastmcp pydantic
```

#### 2.2 Implement Features
Follow the [🐍 FastMCP Core Guide](./reference/fastmcp_server.md) to implement:
*   **Tools** with Pydantic validation.
*   **Resources** for data access.
*   **Prompts** for user guidance.
*   **Authentication** for security.

#### 2.3 Advanced Integration
If integrating into a larger system, see [🏗️ FastMCP Advanced](./reference/fastmcp_advanced_architecture.md) for:
*   Mounting on **FastAPI**.
*   Adding **OpenTelemetry** for observability.
*   Using **Namespaces** to avoid collisions (e.g., `github_create_issue`).

---

### Phase 3: Review and Test

#### 3.1 Code Quality
*   **DRY**: Use dependency injection (`Depends`) for shared clients/db connections.
*   **Type Safety**: Strict Pydantic models for all tool inputs.
*   **Error Handling**: Raise clean exceptions that the client can display meaningfully.

#### 3.2 Build and Test
Use the FastMCP CLI Inspector for interactive debugging:
```bash
fastmcp run server.py:mcp --inspector
```

---

### Phase 4: Create Evaluations

**Load [✅ Evaluation Guide](./reference/evaluation.md) for complete evaluation guidelines.**

#### 4.1 Evaluation Requirements
Create an XML file with `<qa_pair>` elements testing complex, multi-step workflows.

---

# Reference Files

## 📚 Documentation Library

### FastMCP (Python)
*   [🐍 FastMCP Core Guide](./reference/fastmcp_server.md) - **Start Here**. Covers the basics + Auth + Elicitation.
*   [🏗️ FastMCP Advanced](./reference/fastmcp_advanced_architecture.md) - For FastAPI, OTel, Namespaces, and Transforms.
*   Official Docs: `https://gofastmcp.com` (Index: `/llms.txt`)

### Deep Dive Resources (Official)
Use `read_url_content` on these for specific implementation details:
*   **Auth**: `https://gofastmcp.com/servers/auth/authentication.md`
*   **Elicitation**: `https://gofastmcp.com/servers/elicitation.md`
*   **Dependencies**: `https://gofastmcp.com/servers/dependency-injection.md`
*   **Telemetry (OTel)**: `https://gofastmcp.com/servers/telemetry.md`
*   **FastAPI Mount**: `https://gofastmcp.com/integrations/fastapi.md`
*   **Transforms (Namespace)**: `https://gofastmcp.com/servers/transforms/namespace.md`
*   **Transforms (Visibility)**: `https://gofastmcp.com/servers/transforms/visibility.md`
*   **Server Config**: `https://gofastmcp.com/deployment/server-configuration.md`

### Best Practices & Evaluation
*   [📋 MCP Best Practices](./reference/mcp_best_practices.md)
*   [✅ Evaluation Guide](./reference/evaluation.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fzozyurt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

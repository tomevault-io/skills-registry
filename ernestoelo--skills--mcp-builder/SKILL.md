---
name: mcp-builder
description: This skill should be used when the user asks to "create an MCP server", "build an MCP tool", "scaffold MCP", mentions "Model Context Protocol", "MCP integration", or discusses LLM tool servers and external service connectors. Use when this capability is needed.
metadata:
  author: ernestoelo
---

# MCP Builder Skill

Scaffolds, validates, and packages MCP servers (Model Context Protocol) for integrating LLMs with external services.

## When to Use the Skill
- **Building new MCP servers for APIs/services:** Ensure scalability and robust operations.
- **Designing workflows:** Optimize tool usability and discoverability for LLMs.
- **Creating server evaluations:** Develop reliable question-answer pairs to validate server performance.
- **Integrating tools with the MCP protocol:** Adopt best practices for schema design and error handling.

## Usage Guide
### MCP Server Development
#### Analyze and Plan
1. **Research Protocol:** Familiarize with the MCP specification and its core architecture.
   - Use [📋 MCP Best Practices](./references/mcp_best_practices.md) for guidelines.
2. **Select Framework:** Choose between:
   - **TypeScript**: For high compatibility and extensive SDK support.
   - **Python**: For FastAPI-based rapid development.

#### Scaffold and Implement
- **Node.js (TypeScript) Workflow**
```bash
npx mcp-node-init <server-name>
npm install
```
- **Python Workflow**
```bash
fastmcp init <server-name>
```
Set authentication, pagination, and tool input/output schemas as per the MCP requirements.

### Validation and Packaging
1. **Validate MCP Server Implementation**
   - Run `npx @modelcontextprotocol/inspector` for TypeScript.
   - For Python: Use `fastmcp validate`.
2. **Create Evaluations**
   - Refer to [✅ Evaluation Guide](./references/evaluation.md).
   - Example evaluation XML structure:
   ```xml
   <evaluation>
     <qa_pair>
       <question>What is the latest commit on repo X?</question>
       <answer>SHA12345</answer>
     </qa_pair>
   </evaluation>
   ```

## Best Practices
- Use TypeScript SDK or Python for prototyping.
- Plan tool schemas with descriptive names and clear descriptions.
- Implement structured error messages with actionable details.
- Test with MCP Inspector before deployment.
- Design tools for LLM discoverability: concise names, typed schemas, helpful descriptions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ernestoelo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

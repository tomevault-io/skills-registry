---
name: integrate-mcp-server
description: Guides adding a new MCP server integration to AnimalAL-v1: create a nested kernel plugin wrapping the MCP server, add a system message in KernelConstants, register the plugin in StandardKernel, and add integration tests. Use when the user says "add MCP server", "integrate MCP server", "wire MCP tools", or asks how to add or connect a new MCP server to the project. Use when this capability is needed.
metadata:
  author: dezverev
---

# Integrate MCP Server

Guides the agent through adding a new MCP (Model Context Protocol) server integration to AnimalAL-v1. The workflow creates a nested kernel plugin that wraps the MCP server, wires a system message, registers the plugin in StandardKernel, and adds integration tests. Follows the patterns in DOCS/MCPIntegrations and reference implementations (NestedFetchPlugin, NestedFilesystemPlugin).

## When to Apply

- User says: "add MCP server", "integrate MCP server", "wire MCP tools", "add MCP integration for [server]"
- User wants to expose a new MCP server's tools to the kernel via a nested plugin
- User asks how to add or connect a new MCP server to the project
- User refers to an MCP server by name (e.g. fetch, filesystem) and wants it integrated

## Discovery / Gather

Before generating or editing files, gather:

1. **MCP server identity**
   - Server name (e.g. fetch, filesystem) and how it is run (command and args, e.g. `python -m mcp_server_fetch`).
   - If the project has tool descriptors under `mcps/<server>/tools/*.json`, read them to know tool names and parameters.

2. **Plugin name and agent name**
   - Use PascalCase; plugin will be `Nested[Name]Plugin` and agent name in StandardKernel will be "[Name]Agent" (e.g. FetchAgent, FilesystemAgent).

3. **System message**
   - One- or two-sentence instruction for the sub-kernel: what it does, which tools to use, and that it must not fabricate data. See existing constants in `KernelConstants.cs` (e.g. `FetchSubKernelSystemMessage`, `FilesystemSubKernelSystemMessage`).

4. **Tests**
   - At least one prompt that should trigger the new agent, expected tool(s), and required response keywords. Use **add-plugin-test** patterns if creating multiple test cases.

## Workflow

### 1. Discover MCP server and tools

- Identify the MCP server: name, run command, and args (e.g. `python`, `["-m", "mcp_server_fetch"]`).
- If present, read `mcps/<server>/tools/*.json` or `src/Plugins/DOCS/MCPIntegrations/<name>-plugin-integration.md` for tool names and parameters.
- Check `McpCommandValidator` and project conventions (e.g. allowed commands like `python`, `python3`, `dotnet`) so the plugin uses a valid command.

### 2. Create the nested plugin

- Add `src/Plugins/NestedKernel/SK/Nested[Name]Plugin.cs` following **NestedFetchPlugin** or **NestedFilesystemPlugin**.
- Use the **NestedMCPKernelPlugin** example (in `src/Plugins/Examples/NestedKernels/`) as implementation reference; the generic NestedMCPKernelPlugin is **not** registered in StandardKernel: lazy async kernel (`Lazy<Task<Kernel>>`), MCP client lifecycle, disposal, retry for transient failures, thread-safe initialization. New MCP integrations use Nested[Name]Plugin (e.g. NestedFetchPlugin) and register in `AddNestedKernelPlugins`.
- Expose one main function (e.g. `Ask[Name]Agent`) that takes user message and optional `ChatHistory`; pass ChatHistory via kernel arguments if the project uses context injection.
- Ensure the plugin implements `IDisposable` and `IAsyncDisposable` and disposes the MCP client.

Reference files:

- `src/Plugins/NestedKernel/SK/NestedFetchPlugin.cs`
- `src/Plugins/NestedKernel/SK/NestedFilesystemPlugin.cs`
- `src/Plugins/Examples/NestedKernels/NestedMCPKernelPlugin.cs` (example MCP pattern)

### 3. Add system message in KernelConstants

- In `src/Utilities/KernelConstants.cs`, add a new public const string (e.g. `[Name]SubKernelSystemMessage`).
- Content: role + which tools to use + "Always base your entire response on the tool results" + "never invent/fabricate" wording.
- Follow the style of `FetchSubKernelSystemMessage` and `FilesystemSubKernelSystemMessage`.

### 4. Register plugin in StandardKernel

- In `src/AnimalKernel/Core/StandardKernel.cs`, inside `AddNestedKernelPlugins`:
  - Instantiate the new plugin (e.g. `new NestedFetchPlugin(_loggerFactory, _endpoint, _modelId)`), passing any required ctor args.
  - Call `builder.Plugins.AddFromObject(pluginInstance, "[Name]Agent")`.
  - Add to `_pluginInstances["[Name]Agent"] = pluginInstance`.
  - If the plugin is disposable, add it to `_disposablePlugins` for cleanup (follow existing nested plugins in that method).

### 5. Add integration tests

- In `src/IntegrationTesterApp/TestCaseDefinitions.cs`, add test cases that:
  - Use a prompt that should be handled by the new agent.
  - Set expected tools to include the new agent’s main function (e.g. `FetchAgent.AskFetchAgent`).
  - Set forbidden tools to other agents where appropriate.
  - Set required response keywords consistent with the system message and tool behavior.
  - Assign tags (e.g. `fetch`, `filesystem`) for filtered runs.

Use the **add-plugin-test** skill for structure and fields. After adding tests, run the **smart-test-runner** skill (or `.\run-tests.ps1 -t <tag>`) and fix code before relaxing assertions.

## Reference implementations and docs

| Item | Path |
|------|------|
| Fetch MCP integration doc | `src/Plugins/DOCS/MCPIntegrations/fetch-plugin-integration.md` |
| Filesystem MCP integration doc | `src/Plugins/DOCS/MCPIntegrations/filesystem-plugin-integration.md` |
| Nested MCP example plugin | `src/Plugins/Examples/NestedKernels/NestedMCPKernelPlugin.cs` |
| Nested fetch plugin | `src/Plugins/NestedKernel/SK/NestedFetchPlugin.cs` |
| Nested filesystem plugin | `src/Plugins/NestedKernel/SK/NestedFilesystemPlugin.cs` |
| System messages | `src/Utilities/KernelConstants.cs` |
| Plugin registration | `src/AnimalKernel/Core/StandardKernel.cs` (`AddNestedKernelPlugins`) |
| Test definitions | `src/IntegrationTesterApp/TestCaseDefinitions.cs` |

## Checklist before delivering

- [ ] MCP server command/args and tool descriptors (or docs) identified.
- [ ] `Nested[Name]Plugin.cs` created following NestedFetchPlugin/NestedFilesystemPlugin and NestedMCPKernelPlugin pattern; implements IDisposable/IAsyncDisposable.
- [ ] New system message constant added in `KernelConstants.cs` and used by the nested plugin’s internal kernel.
- [ ] Plugin registered in `AddNestedKernelPlugins`: `AddFromObject`, `_pluginInstances`, and `_disposablePlugins` if applicable.
- [ ] At least one integration test added in `TestCaseDefinitions.cs` with expected tool, keywords, and tags.
- [ ] Tests run (e.g. via **smart-test-runner** or `.\run-tests.ps1 -t <tag>`); failures addressed by fixing code, not by weakening assertions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dezverev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

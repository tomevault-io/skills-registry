---
name: mcp-protocol-development
description: This skill should be used when implementing MCP protocol integration, JSON-RPC communication, tool discovery, and MCP server lifecycle management with TypeScript and Node.js Use when this capability is needed.
metadata:
  author: myst4ke
---

# MCP Protocol Development Skill

## Overview

This skill provides specialized guidance for implementing Model Context Protocol (MCP) integration functionality. It covers MCP server lifecycle management, JSON-RPC 2.0 communication, tool discovery via tools/list requests, schema analysis, and MCP subprocess management. This skill is essential for tasks involving MCP server interaction, protocol communication, and tool inspection.

**Technologies**: TypeScript, Node.js, MCP SDK (@modelcontextprotocol/sdk), JSON-RPC 2.0, child_process

**Applicable to US-3 Tasks**: TASK-3.1, TASK-3.2, TASK-3.3, TASK-3.4

## Core Capabilities

- **MCP Server Lifecycle**: Start, monitor, and gracefully shutdown MCP server subprocesses
- **JSON-RPC Communication**: Send requests and parse responses following JSON-RPC 2.0 spec
- **Tool Discovery**: Query MCP for available tools using tools/list protocol
- **Schema Parsing**: Extract and analyze tool input/output schemas
- **Process Management**: Handle MCP subprocess with proper cleanup and error handling
- **Protocol Validation**: Ensure MCP responses conform to expected formats
- **Timeout Handling**: Implement timeouts for unresponsive MCP operations
- **Error Recovery**: Handle MCP startup failures, crashes, and protocol errors

## Input Requirements

**Required:**
- Task details (ID, description, acceptance criteria)
- MCP installation path or configuration
- Target file paths for implementation
- MCP SDK version and protocol specification

**Optional:**
- Existing MCP connection utilities
- Configuration files for MCP settings
- Test fixtures for MCP responses

## Output Format

**Implementation Guidance Includes:**
- TypeScript interface definitions for MCP protocol
- subprocess management code with spawn()
- JSON-RPC request/response handlers
- Tool schema parsing logic
- Error handling wrappers
- Unit test templates for MCP integration
- Example MCP interactions

## Dependencies

**Claude Code Tools Required:**
- Read (for reviewing existing code and specs)
- Write (for creating new files)
- Edit (for modifying existing implementations)
- Bash (for running tests and installs)

**External Dependencies:**
- @modelcontextprotocol/sdk (MCP SDK)
- Node.js child_process module
- TypeScript strict mode
- Testing framework (Jest recommended)

**Related Skills:**
- None (this is a foundational skill for US-3)

## Workflow

### Step 1: Define MCP Data Models (TASK-3.1 pattern)

1. **Create TypeScript interfaces** for MCP protocol types:
   ```typescript
   interface MCPTool {
     name: string;
     description: string;
     inputSchema: JSONSchema;
     outputSchema?: JSONSchema;
   }
   ```

2. **Define JSON-RPC message types**:
   - Request format with id, method, params
   - Response format with id, result/error
   - Error codes and messages

3. **Add validation methods** to interfaces:
   - Schema completeness checks
   - Required field validation
   - Type compatibility verification

4. **Use TypeScript strict mode** for type safety

### Step 2: Implement MCP Server Lifecycle (TASK-3.2 pattern)

1. **Start MCP subprocess** using Node.js spawn():
   ```typescript
   const mcp = spawn('node', [mcpPath], {
     stdio: ['pipe', 'pipe', 'pipe']
   });
   ```

2. **Implement health check**:
   - Send initialization request
   - Wait for ready response
   - Set timeout (30 seconds)

3. **Add retry logic**:
   - Max 3 retries for startup
   - Exponential backoff between retries
   - Clean up failed attempts

4. **Graceful shutdown**:
   - Send termination signal (SIGTERM)
   - Wait for clean exit
   - Force kill (SIGKILL) if needed
   - Prevent zombie processes

5. **Capture stdio**:
   - Log stdout for debugging
   - Capture stderr for errors
   - Parse JSON-RPC from stdout

### Step 3: Implement Tool Discovery (TASK-3.3 pattern)

1. **Send tools/list request**:
   ```typescript
   const request = {
     jsonrpc: '2.0',
     id: generateId(),
     method: 'tools/list',
     params: {}
   };
   ```

2. **Parse response**:
   - Validate JSON-RPC format
   - Extract tools array
   - Create MCPTool objects

3. **Handle edge cases**:
   - Empty tool list → warning
   - Invalid schema → best-effort parsing
   - Timeout → retry once, then fail

4. **Cache results**:
   - Store tools in memory
   - Invalidate on MCP restart

### Step 4: Implement Schema Analysis (TASK-3.4 pattern)

1. **Parse input schemas**:
   - Extract properties and types
   - Identify required vs optional
   - Extract default values
   - Handle nested objects/arrays

2. **Convert JSON Schema to readable format**:
   - Map types (string, number, boolean, object, array)
   - Generate parameter descriptions
   - Format examples

3. **Handle complex types**:
   - Recursive schema traversal for nested
   - Union types (oneOf/anyOf)
   - Array item schemas
   - Circular reference detection

4. **Validate completeness**:
   - Check for missing descriptions
   - Flag unknown types
   - Warn about incomplete schemas

### Step 5: Add Error Handling

1. **MCP startup errors**:
   - File not found → clear error message
   - Permission denied → guide user
   - Port already in use → suggest alternatives

2. **Protocol errors**:
   - Malformed JSON → log and skip
   - Invalid JSON-RPC → fail gracefully
   - Timeout → retry logic

3. **Tool discovery errors**:
   - Method not found → check MCP version
   - Empty response → warn user
   - Schema parse error → partial conversion

### Step 6: Write Tests

1. **Unit tests** (with mocks):
   - Mock MCP subprocess
   - Test JSON-RPC request/response
   - Test schema parsing edge cases
   - Test error handling

2. **Integration tests** (with real or mock MCP):
   - Start MCP and discover tools
   - Handle startup failures
   - Test cleanup on errors

## Example Usage

### Example 1: Implement TASK-3.1 (Data Models)

**Agent Request**: "Use the mcp-protocol-development skill to implement TASK-3.1"

**Skill Provides**:
```typescript
// File: src/models/mcp-tool.ts
interface MCPTool {
  name: string;
  description: string;
  inputSchema: {
    type: 'object';
    properties: Record<string, {
      type: string;
      description?: string;
      default?: any;
    }>;
    required?: string[];
  };
  outputSchema?: {
    type: string;
    description?: string;
  };
}

// Validation method
function isValidMCPTool(tool: any): tool is MCPTool {
  return (
    typeof tool.name === 'string' &&
    typeof tool.description === 'string' &&
    tool.inputSchema?.type === 'object'
  );
}
```

### Example 2: Implement TASK-3.2 (MCP Lifecycle)

**Agent Request**: "Use the mcp-protocol-development skill to implement TASK-3.2"

**Skill Provides**:
```typescript
// File: src/services/mcp-server-manager.ts
import { spawn, ChildProcess } from 'child_process';

class MCPServerManager {
  private process: ChildProcess | null = null;

  async start(mcpPath: string): Promise<void> {
    this.process = spawn('node', [mcpPath], {
      stdio: ['pipe', 'pipe', 'pipe']
    });

    // Set timeout for startup
    const startupPromise = this.waitForReady();
    const timeoutPromise = new Promise((_, reject) =>
      setTimeout(() => reject(new Error('MCP startup timeout')), 30000)
    );

    await Promise.race([startupPromise, timeoutPromise]);
  }

  async shutdown(): Promise<void> {
    if (!this.process) return;

    // Graceful shutdown
    this.process.kill('SIGTERM');

    // Force kill after 5s
    setTimeout(() => {
      if (this.process && !this.process.killed) {
        this.process.kill('SIGKILL');
      }
    }, 5000);
  }

  private async waitForReady(): Promise<void> {
    // Implementation for waiting for MCP ready signal
  }
}
```

### Example 3: Implement TASK-3.3 (Tool Discovery)

**Agent Request**: "Use the mcp-protocol-development skill to implement TASK-3.3"

**Skill Provides**:
```typescript
// File: src/services/mcp-tool-discovery.ts
async function discoverTools(mcpManager: MCPServerManager): Promise<MCPTool[]> {
  const request = {
    jsonrpc: '2.0',
    id: generateRequestId(),
    method: 'tools/list',
    params: {}
  };

  const response = await mcpManager.sendRequest(request);

  if (response.error) {
    throw new Error(`Tool discovery failed: ${response.error.message}`);
  }

  const tools: MCPTool[] = response.result.tools.map(parseTool);
  return tools;
}
```

## Error Handling

### Common Errors and Solutions

**Error: MCP process fails to start**
- Cause: Invalid MCP path, missing dependencies, permissions
- Solution: Validate path exists, check Node.js installed, verify file permissions
- Code: Check spawn error event and provide specific guidance

**Error: JSON-RPC response timeout**
- Cause: MCP hung, network issue, slow operation
- Solution: Implement timeout with retry, increase timeout threshold
- Code: Use Promise.race with timeout promise

**Error: Malformed tool schema**
- Cause: MCP returns invalid JSON Schema, missing fields
- Solution: Best-effort parsing with warnings, use defaults
- Code: Try-catch around schema parsing, log warnings

**Error: Tool discovery returns empty list**
- Cause: MCP has no tools, wrong MCP version, protocol mismatch
- Solution: Warn user, check MCP version, verify protocol compatibility
- Code: Check result.tools.length === 0, provide helpful message

**Error: Zombie MCP processes**
- Cause: Improper cleanup, crash during shutdown
- Solution: Track process IDs, implement cleanup on exit
- Code: Use process.on('exit', cleanup), ensure kill signals sent

## Integration Points

### With parse-spec-structure Skill

Receives task details to implement:
```
task = parsed_spec.tasks.find(t => t.id === 'TASK-3.1')
// Use task.acceptanceCriteria to guide implementation
```

### With Implementation Agents

Backend agents invoke this skill:
```
# Backend agent implementing TASK-3.2
"Use the mcp-protocol-development skill to implement MCP server lifecycle management"
```

### With Testing Skills

Generated code should be tested:
```
# After implementing with this skill
"Use the unit-testing skill to create tests for MCPServerManager"
```

### With Project Architecture

Integrates with:
- src/models/ for data types
- src/services/ for MCP interaction services
- src/utils/ for JSON-RPC helpers
- tests/ for unit and integration tests

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/myst4ke) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

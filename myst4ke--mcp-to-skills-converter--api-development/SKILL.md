---
name: api-development
description: This skill should be used when implementing backend API services, file operations, and process management in TypeScript/Node.js Use when this capability is needed.
metadata:
  author: myst4ke
---

# API Development Skill

## Overview

This skill provides guidance for implementing backend API services with TypeScript and Node.js. It covers file system operations, process management, service architecture, error handling, and cross-platform compatibility. For US-4 (MCP Uninstaller), this skill is specifically tailored for:
- File validation and verification services
- Process detection and termination
- Directory removal with safety checks
- Rollback mechanism implementation

## Core Capabilities

- **Service Architecture**: Create well-structured TypeScript services with clear separation of concerns
- **File System Operations**: Implement safe file/directory operations with proper error handling
- **Process Management**: Detect and terminate processes cross-platform (Windows, macOS, Linux)
- **Error Handling**: Implement comprehensive error handling with specific error types
- **Cross-Platform Support**: Handle platform differences in file paths and process management
- **Async/Await Patterns**: Use modern async patterns for non-blocking operations
- **Type Safety**: Leverage TypeScript for type-safe implementations

## Input Requirements

**Required:**
- Task ID and description
- Files to create or modify
- Acceptance criteria
- Dependencies on other tasks

**Optional:**
- Existing service interfaces
- Project architecture patterns
- Testing requirements

## Output Format

The skill returns:
- Service implementation with TypeScript
- Interface/type definitions
- Error handling patterns
- Usage examples
- Testing guidance

## Dependencies

- **TypeScript 5.x**: For type-safe implementation
- **Node.js 18+**: For runtime environment
- **fs-extra**: Enhanced file system operations
- **child_process**: For process management
- **rimraf** or native **fs.rm**: For recursive directory deletion

## Workflow

### Step 1: Define Service Interface

Create TypeScript interfaces for the service:

```typescript
// src/interfaces/service-name.interface.ts
export interface ServiceResult {
  success: boolean;
  data?: any;
  error?: string;
}

export interface ServiceOptions {
  // Service-specific options
}
```

### Step 2: Create Service Class

Implement the service with proper structure:

```typescript
// src/services/service-name.ts
import { ServiceResult, ServiceOptions } from '../interfaces/service-name.interface';

export class ServiceName {
  constructor(private options?: ServiceOptions) {}

  async execute(): Promise<ServiceResult> {
    try {
      // Implementation
      return { success: true, data: result };
    } catch (error) {
      return {
        success: false,
        error: error instanceof Error ? error.message : 'Unknown error'
      };
    }
  }
}
```

### Step 3: Implement Core Logic

Add the main functionality with proper error handling:

```typescript
private async coreLogic(): Promise<void> {
  // Step 1: Validation
  this.validate();

  // Step 2: Execute operation
  const result = await this.performOperation();

  // Step 3: Verify result
  this.verifyResult(result);
}
```

### Step 4: Add Error Handling

Create specific error types and handling:

```typescript
class ServiceError extends Error {
  constructor(message: string, public code: string) {
    super(message);
    this.name = 'ServiceError';
  }
}

// Usage
throw new ServiceError('Operation failed', 'OPERATION_FAILED');
```

### Step 5: Handle Cross-Platform Differences

Use platform-specific logic when needed:

```typescript
import * as os from 'os';

private getPlatformCommand(): string {
  const platform = os.platform();

  if (platform === 'win32') {
    return 'tasklist';
  } else {
    return 'ps aux';
  }
}
```

### Step 6: Write Unit Tests

Create comprehensive tests:

```typescript
import { ServiceName } from '../services/service-name';

describe('ServiceName', () => {
  it('should execute successfully', async () => {
    const service = new ServiceName();
    const result = await service.execute();
    expect(result.success).toBe(true);
  });
});
```

## Example Usage

### Example 1: File Validation Service (TASK-4.1)

```typescript
// src/services/skill-verifier.ts
import * as fs from 'fs-extra';
import { marked } from 'marked';

export interface SkillVerificationResult {
  isValid: boolean;
  errors: string[];
  toolsFound: string[];
  skillPath: string;
}

export class SkillVerifier {
  async verifySkill(skillPath: string, expectedTools: string[]): Promise<SkillVerificationResult> {
    const errors: string[] = [];
    const toolsFound: string[] = [];

    try {
      // Check file exists
      if (!await fs.pathExists(skillPath)) {
        errors.push(`Skill file not found at ${skillPath}`);
        return { isValid: false, errors, toolsFound, skillPath };
      }

      // Read and parse markdown
      const content = await fs.readFile(skillPath, 'utf-8');
      const tokens = marked.lexer(content);

      // Extract tool names from content
      const toolPattern = /(?:##|###)\s+(\w+[-_]?\w+)/g;
      let match;
      while ((match = toolPattern.exec(content)) !== null) {
        toolsFound.push(match[1]);
      }

      // Verify all expected tools are present
      for (const tool of expectedTools) {
        if (!toolsFound.includes(tool)) {
          errors.push(`Missing tool: ${tool}`);
        }
      }

      // Check file size
      const stats = await fs.stat(skillPath);
      if (stats.size === 0) {
        errors.push('Skill file is empty');
      }

      return {
        isValid: errors.length === 0,
        errors,
        toolsFound,
        skillPath
      };
    } catch (error) {
      errors.push(`Verification failed: ${error instanceof Error ? error.message : 'Unknown error'}`);
      return { isValid: false, errors, toolsFound, skillPath };
    }
  }
}
```

### Example 2: Process Management Service (TASK-4.3)

```typescript
// src/services/process-manager.ts
import { spawn, exec } from 'child_process';
import { promisify } from 'util';
import * as os from 'os';

const execAsync = promisify(exec);

export interface ProcessInfo {
  pid: number;
  name: string;
  command: string;
}

export class ProcessManager {
  private readonly timeout = 5000; // 5 seconds

  async findProcess(processPattern: string): Promise<ProcessInfo | null> {
    const platform = os.platform();

    try {
      if (platform === 'win32') {
        const { stdout } = await execAsync('tasklist');
        const lines = stdout.split('\n');
        for (const line of lines) {
          if (line.toLowerCase().includes(processPattern.toLowerCase())) {
            const parts = line.trim().split(/\s+/);
            return {
              pid: parseInt(parts[1], 10),
              name: parts[0],
              command: line
            };
          }
        }
      } else {
        const { stdout } = await execAsync('ps aux');
        const lines = stdout.split('\n');
        for (const line of lines) {
          if (line.includes(processPattern)) {
            const parts = line.trim().split(/\s+/);
            return {
              pid: parseInt(parts[1], 10),
              name: parts[10],
              command: line
            };
          }
        }
      }
      return null;
    } catch (error) {
      throw new Error(`Process detection failed: ${error instanceof Error ? error.message : 'Unknown error'}`);
    }
  }

  async terminateProcess(pid: number): Promise<boolean> {
    const platform = os.platform();

    try {
      // Try graceful shutdown (SIGTERM)
      if (platform === 'win32') {
        await execAsync(`taskkill /PID ${pid}`);
      } else {
        process.kill(pid, 'SIGTERM');
      }

      // Wait for process to terminate
      const terminated = await this.waitForTermination(pid, this.timeout);

      if (!terminated) {
        // Force kill (SIGKILL)
        if (platform === 'win32') {
          await execAsync(`taskkill /F /PID ${pid}`);
        } else {
          process.kill(pid, 'SIGKILL');
        }

        await this.waitForTermination(pid, 2000);
      }

      return true;
    } catch (error) {
      throw new Error(`Process termination failed: ${error instanceof Error ? error.message : 'Unknown error'}`);
    }
  }

  private async waitForTermination(pid: number, timeout: number): Promise<boolean> {
    const startTime = Date.now();
    while (Date.now() - startTime < timeout) {
      try {
        process.kill(pid, 0); // Check if process exists
        await new Promise(resolve => setTimeout(resolve, 100));
      } catch {
        return true; // Process doesn't exist
      }
    }
    return false;
  }
}
```

### Example 3: Directory Removal Service (TASK-4.4)

```typescript
// src/services/mcp-remover.ts
import * as fs from 'fs-extra';
import * as path from 'path';
import { execSync } from 'child_process';

export interface RemovalResult {
  success: boolean;
  freedSpace: number; // in bytes
  path: string;
  error?: string;
}

export class MCPRemover {
  async removeDirectory(dirPath: string): Promise<RemovalResult> {
    try {
      // Calculate size before deletion
      const size = await this.calculateDirectorySize(dirPath);

      // Check if directory exists
      if (!await fs.pathExists(dirPath)) {
        throw new Error(`Directory not found: ${dirPath}`);
      }

      // Check permissions
      try {
        await fs.access(dirPath, fs.constants.W_OK);
      } catch {
        throw new Error(`Permission denied: ${dirPath}. Try running with elevated privileges.`);
      }

      // Remove directory recursively
      await fs.rm(dirPath, { recursive: true, force: true });

      // Verify removal
      if (await fs.pathExists(dirPath)) {
        throw new Error('Directory removal verification failed');
      }

      return {
        success: true,
        freedSpace: size,
        path: dirPath
      };
    } catch (error) {
      return {
        success: false,
        freedSpace: 0,
        path: dirPath,
        error: error instanceof Error ? error.message : 'Unknown error'
      };
    }
  }

  private async calculateDirectorySize(dirPath: string): Promise<number> {
    try {
      // Cross-platform size calculation
      if (process.platform === 'win32') {
        // Windows: use PowerShell
        const cmd = `powershell -Command "(Get-ChildItem -Path '${dirPath}' -Recurse | Measure-Object -Property Length -Sum).Sum"`;
        const result = execSync(cmd, { encoding: 'utf-8' });
        return parseInt(result.trim(), 10);
      } else {
        // Unix: use du command
        const cmd = `du -sb "${dirPath}" | cut -f1`;
        const result = execSync(cmd, { encoding: 'utf-8' });
        return parseInt(result.trim(), 10);
      }
    } catch (error) {
      // Fallback: recursive calculation
      return await this.recursiveSize(dirPath);
    }
  }

  private async recursiveSize(dirPath: string): Promise<number> {
    let size = 0;
    const items = await fs.readdir(dirPath);

    for (const item of items) {
      const itemPath = path.join(dirPath, item);
      const stats = await fs.stat(itemPath);

      if (stats.isDirectory()) {
        size += await this.recursiveSize(itemPath);
      } else {
        size += stats.size;
      }
    }

    return size;
  }
}
```

## Error Handling

### Common Errors and Solutions

**File Not Found Error**:
```typescript
if (!await fs.pathExists(filePath)) {
  throw new Error(`File not found: ${filePath}. Please check the path and try again.`);
}
```

**Permission Denied Error**:
```typescript
try {
  await fs.access(filePath, fs.constants.W_OK);
} catch {
  throw new Error(`Permission denied: ${filePath}. Try running with elevated privileges or check file permissions.`);
}
```

**Process Not Found Error**:
```typescript
const process = await this.findProcess(pattern);
if (!process) {
  throw new Error(`Process matching "${pattern}" not found. It may have already terminated.`);
}
```

**Cross-Platform Compatibility Error**:
```typescript
const platform = os.platform();
if (!['win32', 'darwin', 'linux'].includes(platform)) {
  throw new Error(`Unsupported platform: ${platform}. This operation is only supported on Windows, macOS, and Linux.`);
}
```

## Integration Points

### With Configuration Skill
- API services may need to read/write configuration files
- Share file system utilities for atomic operations

### With Testing Skills
- Unit tests validate individual service methods
- Integration tests verify service interactions
- Mock file system and process operations in tests

### With CLI Component
- Services are orchestrated by CLI interface
- CLI handles user input and displays service results
- Services should return structured results for CLI formatting

### With Rollback Mechanism
- Services should support transaction-like operations
- Track operations for potential rollback
- Provide rollback methods where applicable

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/myst4ke) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

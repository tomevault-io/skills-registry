---
name: configuration
description: This skill should be used when implementing configuration management, backup systems, and atomic file updates in TypeScript Use when this capability is needed.
metadata:
  author: myst4ke
---

# Configuration Management Skill

## Overview

This skill provides guidance for implementing safe configuration file management including backup systems, atomic updates, and recovery mechanisms. For US-4 (MCP Uninstaller), this skill covers:
- Creating timestamped configuration backups
- Atomic JSON file updates
- Configuration validation
- Backup restoration and cleanup

## Core Capabilities

- **Atomic File Operations**: Write-then-rename pattern for safe file updates
- **Backup Management**: Create, restore, and clean up timestamped backups
- **JSON Manipulation**: Safe parsing, modification, and validation of JSON config files
- **Error Recovery**: Automatic rollback on failures
- **Metadata Tracking**: Include metadata in backups for audit trail
- **Cleanup Policies**: Implement retention policies (e.g., keep last 10 backups)

## Input Requirements

**Required:**
- Configuration file path
- Operation type (backup, update, restore)
- Backup directory path

**Optional:**
- Retention policy (number of backups to keep)
- Validation schema
- Metadata to include in backup

## Output Format

Returns:
- Operation result (success/failure)
- Backup file path (for backup operations)
- Validation results
- Error messages if applicable

## Dependencies

- **fs-extra**: Enhanced file system operations
- **JSON schema validator** (optional): For config validation
- **TypeScript 5.x**: For type-safe config handling

## Workflow

### Step 1: Define Configuration Interfaces

Create TypeScript interfaces for config structure:

```typescript
// src/interfaces/config.interface.ts
export interface ClaudeConfig {
  mcpServers?: Record<string, MCPServerConfig>;
  [key: string]: any;
}

export interface MCPServerConfig {
  command: string;
  args?: string[];
  env?: Record<string, string>;
}

export interface BackupMetadata {
  timestamp: string;
  originalPath: string;
  reason: string;
  mcpName?: string;
}
```

### Step 2: Implement Backup Service

Create backup with metadata:

```typescript
// src/services/config-backup.ts
import * as fs from 'fs-extra';
import * as path from 'path';

export class ConfigBackup {
  private backupDir: string;
  private maxBackups: number;

  constructor(backupDir: string = '.claude/backups', maxBackups: number = 10) {
    this.backupDir = backupDir;
    this.maxBackups = maxBackups;
  }

  async createBackup(configPath: string, reason: string, mcpName?: string): Promise<string> {
    // Ensure backup directory exists
    await fs.ensureDir(this.backupDir);

    // Generate timestamp
    const timestamp = new Date().toISOString().replace(/[:.]/g, '-');
    const backupFileName = `config-backup-${timestamp}.json`;
    const backupPath = path.join(this.backupDir, backupFileName);

    // Read original config
    const config = await fs.readJson(configPath);

    // Create backup with metadata
    const backup = {
      metadata: {
        timestamp: new Date().toISOString(),
        originalPath: configPath,
        reason,
        mcpName
      },
      config
    };

    // Write backup atomically
    await this.atomicWrite(backupPath, JSON.stringify(backup, null, 2));

    // Verify backup
    if (!await fs.pathExists(backupPath)) {
      throw new Error('Backup verification failed');
    }

    // Clean up old backups
    await this.cleanupOldBackups();

    return backupPath;
  }

  private async atomicWrite(filePath: string, content: string): Promise<void> {
    const tempPath = `${filePath}.tmp`;
    await fs.writeFile(tempPath, content, 'utf-8');
    await fs.rename(tempPath, filePath);
  }

  private async cleanupOldBackups(): Promise<void> {
    const files = await fs.readdir(this.backupDir);
    const backupFiles = files
      .filter(f => f.startsWith('config-backup-'))
      .sort()
      .reverse();

    // Keep only the most recent maxBackups
    const filesToDelete = backupFiles.slice(this.maxBackups);
    for (const file of filesToDelete) {
      await fs.remove(path.join(this.backupDir, file));
    }
  }

  async restore(backupPath: string, targetPath: string): Promise<void> {
    const backup = await fs.readJson(backupPath);

    if (!backup.config) {
      throw new Error('Invalid backup file: missing config data');
    }

    // Write config atomically
    await this.atomicWrite(targetPath, JSON.stringify(backup.config, null, 2));
  }
}
```

### Step 3: Implement Configuration Update

Update config with atomic operations:

```typescript
// src/services/config-updater.ts
import * as fs from 'fs-extra';

export class ConfigUpdater {
  async removeMCPEntry(configPath: string, mcpName: string): Promise<void> {
    // Read config
    const config = await fs.readJson(configPath);

    // Remove MCP entry
    if (config.mcpServers && config.mcpServers[mcpName]) {
      delete config.mcpServers[mcpName];
    } else {
      throw new Error(`MCP "${mcpName}" not found in configuration`);
    }

    // Validate resulting config
    this.validateConfig(config);

    // Write atomically
    const tempPath = `${configPath}.tmp`;
    await fs.writeJson(tempPath, config, { spaces: 2 });
    await fs.rename(tempPath, configPath);

    // Verify write
    const written = await fs.readJson(configPath);
    if (JSON.stringify(written) !== JSON.stringify(config)) {
      throw new Error('Configuration verification failed');
    }
  }

  private validateConfig(config: any): void {
    // Basic validation
    if (typeof config !== 'object' || config === null) {
      throw new Error('Invalid config: must be an object');
    }

    // Validate mcpServers if present
    if (config.mcpServers) {
      if (typeof config.mcpServers !== 'object') {
        throw new Error('Invalid config: mcpServers must be an object');
      }

      for (const [name, server] of Object.entries(config.mcpServers)) {
        if (typeof server !== 'object' || server === null) {
          throw new Error(`Invalid config: mcpServers.${name} must be an object`);
        }
      }
    }
  }

  async updateWithRetry(configPath: string, mcpName: string, maxAttempts: number = 3): Promise<void> {
    let lastError: Error | null = null;

    for (let attempt = 1; attempt <= maxAttempts; attempt++) {
      try {
        await this.removeMCPEntry(configPath, mcpName);
        return; // Success
      } catch (error) {
        lastError = error instanceof Error ? error : new Error('Unknown error');

        if (error instanceof Error && error.message.includes('EBUSY')) {
          // File is locked, wait and retry
          await new Promise(resolve => setTimeout(resolve, 1000 * attempt));
        } else {
          // Non-retryable error
          throw error;
        }
      }
    }

    throw new Error(`Config update failed after ${maxAttempts} attempts: ${lastError?.message}`);
  }
}
```

### Step 4: Integrate with Rollback

Support transaction-like operations:

```typescript
// src/services/config-transaction.ts
export class ConfigTransaction {
  private backup: ConfigBackup;
  private updater: ConfigUpdater;
  private backupPath?: string;

  constructor() {
    this.backup = new ConfigBackup();
    this.updater = new ConfigUpdater();
  }

  async execute(configPath: string, mcpName: string): Promise<void> {
    try {
      // Step 1: Create backup
      this.backupPath = await this.backup.createBackup(
        configPath,
        `Removing MCP: ${mcpName}`,
        mcpName
      );

      // Step 2: Update config
      await this.updater.removeMCPEntry(configPath, mcpName);

    } catch (error) {
      // Rollback on error
      if (this.backupPath) {
        await this.backup.restore(this.backupPath, configPath);
      }
      throw error;
    }
  }

  async rollback(configPath: string): Promise<void> {
    if (!this.backupPath) {
      throw new Error('No backup available for rollback');
    }

    await this.backup.restore(this.backupPath, configPath);
  }
}
```

## Example Usage

### Example 1: Create Backup (TASK-4.2)

```typescript
import { ConfigBackup } from './services/config-backup';

const backup = new ConfigBackup('.claude/backups', 10);

// Create backup before modifications
const backupPath = await backup.createBackup(
  '~/.claude/claude_desktop_config.json',
  'Pre-uninstall backup',
  'server-filesystem'
);

console.log(`Backup created: ${backupPath}`);
```

### Example 2: Update Configuration (TASK-4.5)

```typescript
import { ConfigUpdater } from './services/config-updater';

const updater = new ConfigUpdater();

// Remove MCP entry with retry logic
await updater.updateWithRetry(
  '~/.claude/claude_desktop_config.json',
  'server-filesystem',
  3 // max retries
);

console.log('MCP entry removed successfully');
```

### Example 3: Transactional Update

```typescript
import { ConfigTransaction } from './services/config-transaction';

const transaction = new ConfigTransaction();

try {
  // Execute with automatic backup
  await transaction.execute(
    '~/.claude/claude_desktop_config.json',
    'server-filesystem'
  );

  console.log('Configuration updated successfully');
} catch (error) {
  console.error('Update failed, rollback performed:', error);
}
```

## Error Handling

### File Locked Error

```typescript
if (error.code === 'EBUSY' || error.code === 'EPERM') {
  throw new Error('Configuration file is locked. Close Claude Code and try again.');
}
```

### Malformed JSON Error

```typescript
try {
  const config = await fs.readJson(configPath);
} catch (error) {
  throw new Error(`Invalid JSON in config file: ${error.message}. Please fix manually or restore from backup.`);
}
```

### Backup Restore Error

```typescript
if (!await fs.pathExists(backupPath)) {
  throw new Error(`Backup file not found: ${backupPath}. Cannot perform rollback.`);
}
```

## Integration Points

### With API Services
- Backup service is used by configuration updater
- Both used by rollback mechanism

### With Rollback Mechanism
- Provides backup/restore capabilities
- Supports transaction-like operations

### With CLI Interface
- CLI orchestrates backup and update operations
- Displays backup paths and success messages

### With Testing
- Mock file system for unit tests
- Test atomic operations and rollback

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/myst4ke) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

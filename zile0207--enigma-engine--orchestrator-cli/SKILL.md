---
name: orchestrator-cli
description: Orchestrator CLI Expert Use when this capability is needed.
metadata:
  author: zile0207
---

# Specialty: Orchestrator CLI Expert

## Persona

You are a Systems Engineer building the standalone Enigma CLI distribution tool—the RaaS delivery mechanism that empowers designers to ship design systems to developers via branded CLIs like `npx company-ui add button`.

You understand that this CLI is the critical bridge between designer workspaces and developer workflows. Every command must be reliable, fast, and provide excellent UX while respecting the designer's brand identity (white-label experience).

---

## Core Responsibilities

### 1. CLI Command Architecture

**Built on Commander.js** - You implement all CLI commands using the `commander` library for consistent argument parsing, help generation, and command structure.

**Primary Commands**:

```bash
# Component installation (core RaaS flow)
npx company-ui add <component>[@<version>]
npx company-ui update <component>

# Registry initialization
npx company-ui init

# Authentication
npx company-ui login
npx company-ui logout

# Discovery
npx company-ui list
npx company-ui info <component>

# Dependency management
npx company-ui install-deps
```

**Command Design Principles**:

- Single responsibility per command
- Consistent error messages branded to registry name
- Helpful suggestions when commands fail
- Progress indicators with `ora` for long-running operations
- Colored output with `chalk` for clarity (registry theme colors)

### 2. Ejection Engine (File System Operations)

**You use `fs-extra`** for robust file system operations that handle edge cases (permissions, path normalization, conflict resolution).

**Component Ejection Flow**:

```typescript
// When developer runs: npx company-ui add button
async function ejectComponent(componentName: string, version?: string) {
  // 1. Fetch from Enigma API
  const component = await api.getComponent(
    registryName,
    componentName,
    version
  );

  // 2. Validate schema
  validateComponentSchema(component);

  // 3. Resolve target paths
  const basePath = path.join(process.cwd(), "ui");
  const componentPath = path.join(basePath, componentName);

  // 4. Create directory structure
  await fs.ensureDir(componentPath);

  // 5. Eject all files (TSX + CSS + type definitions)
  for (const file of component.files) {
    const filePath = path.join(componentPath, file.path);
    await fs.ensureDir(path.dirname(filePath));
    await fs.writeFile(filePath, transformImports(file.content));
  }

  // 6. Inject theme variables into globals.css
  await injectThemeVariables(component.theme);

  // 7. Install peer dependencies
  await installDependencies(component.dependencies);

  // 8. Update component manifest (for future updates)
  await updateComponentManifest(componentName, component);
}
```

**File Conflict Resolution**:

- If file exists: Warn user with `--overwrite` flag option
- Preserve user modifications when possible (merge strategy for updates)
- Create backup files before overwriting (`.bak` extension)

**Import Path Rewriting**:

```typescript
// Designer's registry uses: @enigma/ui/button
// Developer's project uses: @/components/ui/button
function transformImports(code: string, aliases: PathAliases): string {
  return code
    .replace(/@enigma\/ui\/([^"']+)/g, (match, path) => {
      return `@/components/ui/${path}`;
    })
    .replace(/from '([^']+)'/g, (match, importPath) => {
      return aliases[importPath] ? `from '${aliases[importPath]}'` : match;
    });
}
```

### 3. Dependency Resolution Engine

**You detect and install dependencies** automatically when developers add components. This is critical for friction-free DX.

**Dependency Resolution Flow**:

```typescript
async function installDependencies(
  dependencies: string[],
  packageManager: PackageManager
) {
  if (!dependencies || dependencies.length === 0) return;

  // Check existing dependencies
  const existingDeps = await readPackageJson();
  const missingDeps = dependencies.filter((dep) => !existingDeps[dep]);

  if (missingDeps.length === 0) {
    console.log("All dependencies already installed");
    return;
  }

  // Ask user for confirmation
  const confirmed = await promptInstall(missingDeps);
  if (!confirmed) return;

  // Detect package manager (npm/yarn/pnpm)
  const manager = detectPackageManager() || packageManager || "npm";

  // Install dependencies
  const command = getInstallCommand(manager, missingDeps);
  await exec(command);

  console.log("Dependencies installed successfully");
}

function detectPackageManager(): PackageManager | null {
  if (fs.existsSync("pnpm-lock.yaml")) return "pnpm";
  if (fs.existsSync("yarn.lock")) return "yarn";
  if (fs.existsSync("package-lock.json")) return "npm";
  return null;
}

function getInstallCommand(manager: PackageManager, deps: string[]): string {
  switch (manager) {
    case "pnpm":
      return `pnpm add ${deps.join(" ")}`;
    case "yarn":
      return `yarn add ${deps.join(" ")}`;
    case "npm":
      return `npm install ${deps.join(" ")}`;
  }
}
```

**Dependency Categories**:

1. **Runtime dependencies** (e.g., `lucide-react`, `framer-motion`)
2. **Peer dependencies** (e.g., `react`, `react-dom`, `clsx`, `tailwind-merge`)
3. **Dev dependencies** (e.g., `@types/react`)

**Dependency Conflict Resolution**:

- Check version constraints (ranges in package.json)
- Warn about potential conflicts
- Suggest resolutions (e.g., "Update tailwindcss to ^3.4.0")

### 4. Theme Variable Injection

**You inject design tokens** into the developer's `globals.css` automatically when they install components.

**Theme Injection Flow**:

```typescript
async function injectThemeVariables(themeConfig: ThemeConfig) {
  const globalsCssPath = path.join(process.cwd(), "src/app/globals.css");

  // 1. Read existing globals.css
  let content = await fs.readFile(globalsCssPath, "utf8");

  // 2. Check if Enigma theme section exists
  const enigmaSectionRegex =
    /\/\* --- ENIGMA THEME START --- \*\/[\s\S]*?\/\* --- ENIGMA THEME END --- \*\//;

  if (enigmaSectionRegex.test(content)) {
    // Update existing section
    content = content.replace(
      enigmaSectionRegex,
      generateThemeBlock(themeConfig)
    );
  } else {
    // Append new section
    content += "\n\n" + generateThemeBlock(themeConfig);
  }

  // 3. Write back
  await fs.writeFile(globalsCssPath, content);
}

function generateThemeBlock(themeConfig: ThemeConfig): string {
  const { colors, spacing, typography } = themeConfig;

  return `/* --- ENIGMA THEME START --- */
@theme {
${Object.entries(colors)
  .map(([key, value]) => `  --color-${key}: ${value};`)
  .join("\n")}
${Object.entries(spacing)
  .map(([key, value]) => `  --spacing-${key}: ${value};`)
  .join("\n")}
${Object.entries(typography)
  .map(([key, value]) => `  --font-${key}: ${value};`)
  .join("\n")}
}
/* --- ENIGMA THEME END --- */`;
}
```

**Theme Update Strategy**:

- Append if not exists
- Replace if exists (with warning)
- Preserve user's custom CSS outside Enigma section
- Support multiple registry themes (if developer uses multiple registries)

### 5. Version Management System

**You handle two-tier versioning**: Component versions and CLI package versions.

**Component Version Resolution**:

```typescript
async function resolveComponentVersion(
  componentName: string,
  versionSpecifier: string
): Promise<Component> {
  // Parse version specifier
  // 1. No version -> latest
  // 2. Specific version (1.2.0) -> exact match
  // 3. Range (^1.2.0, ~1.2.0) -> find best match
  // 4. Tag (latest, beta) -> resolve tag to version

  if (!versionSpecifier) {
    return await api.getLatestComponent(componentName);
  }

  if (isSemVer(versionSpecifier)) {
    return await api.getComponentVersion(componentName, versionSpecifier);
  }

  if (isSemVerRange(versionSpecifier)) {
    return await api.resolveVersionRange(componentName, versionSpecifier);
  }

  throw new Error(`Invalid version specifier: ${versionSpecifier}`);
}
```

**Update Workflow**:

```typescript
async function updateComponent(componentName: string, options: UpdateOptions) {
  // 1. Read current installed version from manifest
  const manifest = await readComponentManifest(componentName);
  const currentVersion = manifest.version;

  // 2. Fetch available versions
  const versions = await api.getComponentVersions(componentName);

  // 3. Check for updates
  const latestVersion = versions.find((v) => v.isLatest);

  if (currentVersion === latestVersion.version) {
    console.log("Already up to date");
    return;
  }

  // 4. Show changelog
  console.log(`Update available: ${currentVersion} → ${latestVersion.version}`);
  console.log(latestVersion.changelog);

  // 5. Confirm update
  const confirmed = await prompt("Update?", { default: false });
  if (!confirmed) return;

  // 6. Backup existing files (for rollback)
  await backupComponent(componentName);

  // 7. Download new version
  await ejectComponent(componentName, latestVersion.version);

  // 8. Preserve user modifications (if any)
  if (options.preserveChanges) {
    await mergeUserChanges(componentName, manifest.userChanges);
  }

  console.log("Update successful");
}
```

**Rollback Support**:

```typescript
async function rollbackComponent(componentName: string, toVersion?: string) {
  const manifest = await readComponentManifest(componentName);
  const targetVersion = toVersion || manifest.previousVersion;

  await ejectComponent(componentName, targetVersion);
  console.log(`Rolled back to version ${targetVersion}`);
}
```

### 6. Registry Schema Validation

**You validate every component manifest** against the shared `@enigma/registry-schema` before installation to prevent corruption.

```typescript
import { registryItemSchema, type RegistryItem } from "@enigma/registry-schema";

async function validateComponentSchema(component: any): Promise<RegistryItem> {
  try {
    const validated = registryItemSchema.parse(component);

    // Additional validations
    if (!validated.files || validated.files.length === 0) {
      throw new Error("Component must have at least one file");
    }

    const hasTsx = validated.files.some((f) => f.path.endsWith(".tsx"));
    const hasCss = validated.files.some((f) => f.path.endsWith(".css"));

    if (!hasTsx) {
      throw new Error("Component must have at least one .tsx file");
    }

    // Validate file paths are valid
    for (const file of validated.files) {
      if (file.path.includes("..")) {
        throw new Error("Invalid file path: relative traversal not allowed");
      }
      if (file.path.startsWith("/")) {
        throw new Error("Invalid file path: absolute paths not allowed");
      }
    }

    return validated;
  } catch (error) {
    if (error instanceof z.ZodError) {
      const details = error.errors
        .map((e) => `${e.path.join(".")}: ${e.message}`)
        .join("\n");
      throw new Error(`Schema validation failed:\n${details}`);
    }
    throw error;
  }
}
```

**Validation Checks**:

1. Zod schema validation (types, required fields)
2. File structure validation (must have TSX, optional CSS)
3. Path security validation (no traversal, no absolute paths)
4. Dependency format validation (valid npm package names)
5. Theme config validation (valid colors, spacing values)

### 7. CLI Generation Engine

**You generate branded CLI packages** from designer registries—this is the core RaaS feature that allows `npx company-ui` distribution.

**CLI Generation Flow** (Designer side in Enigma UI, but you need to understand it):

```typescript
// When designer clicks "Generate CLI" in Enigma dashboard
async function generateCLIPackage(registryId: string, cliName: string) {
  // 1. Fetch registry configuration
  const registry = await api.getRegistry(registryId);

  // 2. Create CLI package structure
  const cliPackage = {
    name: cliName, // e.g., "company-ui"
    version: "1.0.0",
    type: "module",
    bin: {
      [cliName]: "./dist/index.js", // e.g., "company-ui": "./dist/index.js"
    },
    dependencies: {
      "@enigma/cli-core": "latest", // Shared CLI core
      commander: "^12.0.0",
      axios: "^1.13.2",
      chalk: "^5.3.0",
      ora: "^8.0.0",
      "fs-extra": "^11.3.3",
      prompts: "^2.4.2",
    },
    description: registry.description,
    author: registry.owner,
    license: "MIT",
  };

  // 3. Generate CLI entry point
  const cliEntryCode = generateCLIEntryCode(registry);

  // 4. Package and publish to npm
  await publishToNpm(cliPackage, cliEntryCode);

  return {
    packageName: cliName,
    installCommand: `npx ${cliName} add <component>`,
    registryUrl: registry.slug,
  };
}

function generateCLIEntryCode(registry: Registry): string {
  return `#!/usr/bin/env node
import { createCLIProgram } from '@enigma/cli-core';

const program = createCLIProgram({
  name: '${registry.cliName}',
  description: '${registry.description}',
  registryId: '${registry.id}',
  apiUrl: 'https://api.enigma-engine.com/v1/${registry.slug}',
  theme: ${JSON.stringify(registry.themeConfig)}
});

program.parse();`;
}
```

**CLI Core Architecture**:

```typescript
// packages/cli-core (shared by all generated CLIs)
export function createCLIProgram(config: CLIConfig): Command {
  const program = new Command();

  program.name(config.name).description(config.description).version("1.0.0");

  // Branded colors from registry theme
  const colors = config.theme.colors;
  chalk.level = 1;
  const primary = chalk.hex(colors.primary || "#3b82f6");
  const success = chalk.hex(colors.success || "#22c55e");
  const error = chalk.hex(colors.error || "#ef4444");

  // Add command: Add component
  program
    .command("add <component>")
    .option("-v, --version <version>", "Specific version to install")
    .option("-o, --overwrite", "Overwrite existing files")
    .action(async (component, options) => {
      await addComponent(component, options, config);
    });

  // Add command: Update component
  program
    .command("update <component>")
    .option("--preserve", "Preserve local modifications")
    .action(async (component, options) => {
      await updateComponent(component, options, config);
    });

  // Add command: List components
  program.command("list").action(async () => {
    await listComponents(config);
  });

  // Add command: Init registry
  program.command("init").action(async () => {
    await initRegistry(config);
  });

  return program;
}
```

**White-Label Experience**:

- CLI name is registry name (`npx company-ui`, `npx marketing-site`)
- All output references registry name, not Enigma
- Help text uses registry description
- Error messages branded to registry
- Theme colors applied to all terminal output

### 8. Multi-Registry Support

**You support developers using multiple registries** (e.g., one for company design system, one for marketing site, one for internal tools).

```typescript
// ~/.config/company-ui/config.json
interface CLIConfig {
  registries: RegistryConfig[];
  defaultRegistry?: string;
  currentRegistry?: string;
}

interface RegistryConfig {
  name: string; // e.g., "company-ui"
  apiUrl: string; // e.g., "https://api.enigma-engine.com/v1/company-ui"
  theme: ThemeConfig;
  lastSync?: string;
}

// Commands can switch between registries
program
  .command("use <registry>")
  .description("Switch to a different registry")
  .action(async (registryName) => {
    const config = await readConfig();
    const registry = config.registries.find((r) => r.name === registryName);

    if (!registry) {
      console.error(`Registry "${registryName}" not found`);
      process.exit(1);
    }

    config.currentRegistry = registryName;
    await writeConfig(config);

    console.log(`Switched to registry: ${registryName}`);
  });

// Commands can specify registry via flag
program
  .command("add <component>")
  .option("-r, --registry <name>", "Use specific registry")
  .action(async (component, options) => {
    const config = await readConfig();
    const registryName =
      options.registry || config.currentRegistry || config.defaultRegistry;
    const registry = config.registries.find((r) => r.name === registryName);

    await addComponent(component, { registry });
  });
```

### 9. Authentication System

**You handle authentication** for private registries and designer workspaces.

```typescript
program
  .command("login")
  .description("Authenticate with Enigma")
  .action(async () => {
    const email = await prompts({
      type: "text",
      name: "email",
      message: "Email:",
      validate: (value) => /.+@.+\..+/.test(value) || "Invalid email",
    });

    const password = await prompts({
      type: "password",
      name: "password",
      message: "Password:",
    });

    try {
      const token = await api.login(email, password);

      // Store token securely
      await storeAuthToken(token);

      console.log("Logged in successfully");
    } catch (error) {
      console.error("Login failed:", error.message);
    }
  });

async function storeAuthToken(token: string) {
  const configPath = path.join(
    os.homedir(),
    ".config",
    "company-ui",
    "auth.json"
  );
  await fs.ensureDir(path.dirname(configPath));
  await fs.writeFile(configPath, JSON.stringify({ token }), { mode: 0o600 });
}

async function getAuthToken(): Promise<string> {
  const configPath = path.join(
    os.homedir(),
    ".config",
    "company-ui",
    "auth.json"
  );
  const auth = JSON.parse(await fs.readFile(configPath, "utf8"));
  return auth.token;
}
```

**Token Management**:

- Store in `~/.config/{cli-name}/auth.json` with restricted permissions (0o600)
- Handle token expiration and refresh
- Support multiple authenticated sessions

### 10. Error Handling & User Feedback

**You provide excellent error messages** that guide developers toward resolution.

```typescript
async function addComponent(
  componentName: string,
  options: AddOptions,
  config: CLIConfig
) {
  const spinner = ora(`Fetching ${componentName}...`).start();

  try {
    // Fetch component
    const component = await api.getComponent(config.apiUrl, componentName);
    spinner.succeed("Component fetched");

    // Validate
    spinner.start("Validating component schema...");
    validateComponentSchema(component);
    spinner.succeed("Schema validated");

    // Check for conflicts
    if ((await componentExists(componentName)) && !options.overwrite) {
      spinner.fail();
      console.error(`Component "${componentName}" already exists.`);
      console.log("Use --overwrite to replace existing files.");
      process.exit(1);
    }

    // Eject
    spinner.start("Ejecting files...");
    await ejectComponentFiles(component);
    spinner.succeed("Files ejected");

    // Install dependencies
    if (component.dependencies?.length > 0) {
      spinner.start("Installing dependencies...");
      await installDependencies(component.dependencies);
      spinner.succeed("Dependencies installed");
    }

    // Inject theme
    spinner.start("Injecting theme variables...");
    await injectThemeVariables(component.theme);
    spinner.succeed("Theme injected");

    console.log(chalk.green(`✓ Successfully installed ${componentName}`));
    console.log(
      `\nUsage:\n  import { ${pascalCase(componentName)} } from '@/components/ui/${componentName}';`
    );
  } catch (error) {
    spinner.fail();

    if (error instanceof NetworkError) {
      console.error(chalk.red("Network error: Could not connect to registry"));
      console.log(chalk.yellow("Check your internet connection and try again"));
    } else if (error instanceof ValidationError) {
      console.error(chalk.red("Validation error:"));
      console.log(error.details);
    } else if (error instanceof AuthError) {
      console.error(chalk.red("Authentication required"));
      console.log(chalk.yellow("Run: npx " + config.name + " login"));
    } else {
      console.error(chalk.red("Error:"), error.message);
    }

    process.exit(1);
  }
}
```

**Error Categories**:

1. **Network errors** (connection issues, timeout)
2. **Authentication errors** (invalid token, expired token)
3. **Validation errors** (schema failures, invalid data)
4. **File system errors** (permissions, disk space)
5. **Dependency errors** (conflicts, install failures)
6. **User errors** (invalid arguments, missing flags)

---

## Implementation Roadmap

### Phase 1: Core CLI (Current)

- [x] Basic `add` command implementation
- [x] File ejection with `fs-extra`
- [ ] Schema validation with `@enigma/registry-schema`
- [ ] Error handling and user feedback
- [ ] Progress indicators with `ora`
- [ ] Colored output with `chalk`

### Phase 2: Dependency Management

- [ ] Auto-detect package manager (npm/yarn/pnpm)
- [ ] Install dependencies automatically
- [ ] Handle dependency conflicts
- [ ] Prompt for confirmation before install
- [ ] Support devDependencies

### Phase 3: Theme Injection

- [ ] Parse theme config from registry
- [ ] Generate Tailwind 4 theme variables
- [ ] Inject into `globals.css`
- [ ] Preserve existing theme sections
- [ ] Support multiple registry themes

### Phase 4: Version Management

- [ ] Resolve semantic versions
- [ ] Support version ranges (^, ~)
- [ ] Implement `update` command
- [ ] Backup before update
- [ ] Rollback support
- [ ] Changelog display

### Phase 5: Import Rewriting

- [ ] Detect project import aliases (tsconfig.json)
- [ ] Transform registry imports to project imports
- [ ] Support custom alias configurations
- [ ] Handle relative imports
- [ ] Update import statements after update

### Phase 6: CLI Generation

- [ ] Generate branded CLI packages
- [ ] Create CLI entry point with registry config
- [ ] Package CLI for npm distribution
- [ ] Publish to npm (or private registry)
- [ ] White-label all CLI outputs

### Phase 7: Authentication

- [ ] Implement `login`/`logout` commands
- [ ] Store auth tokens securely
- [ ] Handle token refresh
- [ ] Support private registries
- [ ] Multi-user authentication

### Phase 8: Multi-Registry Support

- [ ] Registry configuration file
- [ ] Switch between registries
- [ ] Specify registry via flags
- [ ] Multiple authenticated sessions
- [ ] Registry discovery

### Phase 9: Advanced Features

- [ ] `init` command (bootstrap project)
- [ ] `list` command (browse components)
- [ ] `info` command (component details)
- [ ] Search/filter components
- [ ] Interactive prompts with `prompts`

### Phase 10: Developer Experience

- [ ] Comprehensive error messages
- [ ] Helpful suggestions
- [ ] Auto-completion (bash/zsh)
- [ ] Telemetry (opt-in)
- [ ] Debug mode for troubleshooting

---

## Testing Strategy

### Unit Tests

```typescript
describe("CLI - add command", () => {
  it("should eject component files", async () => {
    const mockComponent = createMockComponent();
    await ejectComponent("button", mockComponent);

    expect(fs.writeFile).toHaveBeenCalledWith(
      path.join(process.cwd(), "ui/button/button.tsx"),
      mockComponent.files[0].content
    );
  });

  it("should transform imports", () => {
    const input = `import { Button } from '@enigma/ui/button';`;
    const output = transformImports(input, { "@enigma/ui": "@/components/ui" });

    expect(output).toBe(`import { Button } from '@/components/ui/button';`);
  });

  it("should validate component schema", () => {
    const invalidComponent = { name: "", files: [] };

    expect(() => validateComponentSchema(invalidComponent)).toThrow();
  });
});
```

### Integration Tests

```typescript
describe("CLI - end-to-end flow", () => {
  it("should add component and install dependencies", async () => {
    // Mock API responses
    api.getLatestComponent.mockResolvedValue(createMockComponent());

    // Run CLI command
    await exec("npx company-ui add button");

    // Verify files were ejected
    expect(fs.existsSync("ui/button/button.tsx")).toBe(true);

    // Verify dependencies were installed
    expect(exec).toHaveBeenCalledWith("npm install lucide-react");
  });
});
```

### Manual Testing Checklist

- [ ] `npx company-ui add button` - Basic add
- [ ] `npx company-ui add button@1.0.0` - Add specific version
- [ ] `npx company-ui add button --overwrite` - Overwrite existing
- [ ] `npx company-ui update button` - Update to latest
- [ ] `npx company-ui list` - List available components
- [ ] `npx company-ui info button` - Show component details
- [ ] `npx company-ui init` - Initialize project
- [ ] Error handling for network failures
- [ ] Error handling for invalid component names
- [ ] Theme injection works correctly

---

## Key Dependencies

```json
{
  "dependencies": {
    "@enigma/registry-schema": "workspace:*", // Schema validation
    "axios": "^1.13.2", // HTTP client
    "chalk": "^5.3.0", // Terminal colors
    "commander": "^12.0.0", // CLI framework
    "fs-extra": "^11.3.3", // File system operations
    "ora": "^8.0.0", // Spinners/progress
    "prompts": "^2.4.2", // Interactive prompts
    "zod": "^3.22.0" // Schema validation
  }
}
```

---

## Design Principles

1. **Developer-First UX** - Fast, reliable, minimal friction
2. **White-Label Experience** - Designer's brand, not Enigma's
3. **Fail Gracefully** - Helpful error messages, recovery options
4. **Explicit Over Implicit** - Prompt before destructive operations
5. **Performance** - Lazy loading, caching, parallel operations
6. **Security** - Validate all inputs, sanitize paths, secure auth storage
7. **Extensibility** - Plugin architecture for custom commands
8. **Consistency** - Same patterns across all commands
9. **Testability** - Modular design, dependency injection
10. **Documentation** - Comprehensive help, examples

---

## Your Query Restated

**Your original request**: Expand the orchestrator-cli skills file with expert-level details about the CLI implementation work.

**What I provided**: A comprehensive guide covering:

- CLI command architecture (Commander.js)
- Ejection engine (fs-extra operations)
- Dependency resolution (npm/yarn/pnpm)
- Theme variable injection (Tailwind 4)
- Version management (semantic versioning)
- Registry schema validation (Zod)
- CLI generation engine (white-label CLIs)
- Multi-registry support
- Authentication system
- Error handling patterns
- Implementation roadmap (10 phases)
- Testing strategy

This is the complete blueprint for building the RaaS delivery mechanism that transforms designer workspaces into branded CLIs for developers.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zile0207) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

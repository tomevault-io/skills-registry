---
name: readme-generator
description: Эксперт по README файлам. Используй для создания документации проектов, badges, installation guides и usage examples. Use when this capability is needed.
metadata:
  author: neversight
---

# README Generator

Expert in creating comprehensive, well-structured README files with proper formatting, sections, badges, and documentation best practices.

## README Template

```markdown
# Project Name

[![Build Status](https://img.shields.io/github/actions/workflow/status/owner/repo/ci.yml?branch=main)](https://github.com/owner/repo/actions)
[![npm version](https://img.shields.io/npm/v/package-name.svg)](https://www.npmjs.com/package/package-name)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Coverage](https://img.shields.io/codecov/c/github/owner/repo)](https://codecov.io/gh/owner/repo)

> Brief, compelling description of what the project does and why it's useful.

## Features

- ✅ Feature one with brief explanation
- ✅ Feature two with brief explanation
- ✅ Feature three with brief explanation
- 🚧 Upcoming feature (in development)

## Quick Start

\`\`\`bash
# Install
npm install package-name

# Run
npx package-name init
\`\`\`

## Installation

### Prerequisites

- Node.js 18+
- npm 9+ or yarn 1.22+

### npm

\`\`\`bash
npm install package-name
\`\`\`

### yarn

\`\`\`bash
yarn add package-name
\`\`\`

### pnpm

\`\`\`bash
pnpm add package-name
\`\`\`

## Usage

### Basic Example

\`\`\`javascript
import { Client } from 'package-name';

const client = new Client({
  apiKey: process.env.API_KEY
});

const result = await client.doSomething({
  input: 'Hello, World!'
});

console.log(result);
\`\`\`

### Advanced Configuration

\`\`\`javascript
const client = new Client({
  apiKey: process.env.API_KEY,
  timeout: 30000,
  retries: 3,
  debug: process.env.NODE_ENV === 'development'
});
\`\`\`

## API Reference

### `Client(options)`

Creates a new client instance.

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `apiKey` | `string` | Yes | - | Your API key |
| `timeout` | `number` | No | `10000` | Request timeout in ms |
| `retries` | `number` | No | `0` | Number of retry attempts |

### `client.doSomething(params)`

Performs the main operation.

**Parameters:**

- `input` (string, required): The input to process
- `options` (object, optional): Additional options

**Returns:** `Promise<Result>`

**Example:**

\`\`\`javascript
const result = await client.doSomething({
  input: 'test',
  options: { format: 'json' }
});
\`\`\`

## Configuration

Create a `config.json` file in your project root:

\`\`\`json
{
  "apiKey": "${API_KEY}",
  "environment": "production",
  "features": {
    "caching": true,
    "logging": false
  }
}
\`\`\`

### Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| `API_KEY` | Your API key | Yes |
| `DEBUG` | Enable debug mode | No |
| `LOG_LEVEL` | Logging level (info, warn, error) | No |

## Examples

See the [examples](./examples) directory for more detailed examples:

- [Basic Usage](./examples/basic.js)
- [With TypeScript](./examples/typescript.ts)
- [Error Handling](./examples/error-handling.js)
- [Custom Configuration](./examples/custom-config.js)

## Troubleshooting

### Common Issues

**"Authentication failed" error**

Ensure your API key is valid and has the required permissions.

\`\`\`bash
# Verify your API key
curl -H "Authorization: Bearer $API_KEY" https://api.example.com/verify
\`\`\`

**"Module not found" error**

Make sure you have installed all dependencies:

\`\`\`bash
rm -rf node_modules package-lock.json
npm install
\`\`\`

## Contributing

Contributions are welcome! Please read our [Contributing Guide](CONTRIBUTING.md) for details.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## Development

\`\`\`bash
# Clone the repo
git clone https://github.com/owner/repo.git
cd repo

# Install dependencies
npm install

# Run tests
npm test

# Run in development mode
npm run dev
\`\`\`

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for a list of changes.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- [Library Name](https://example.com) - For providing X functionality
- [Person Name](https://github.com/person) - For their contributions

---

Made with ❤️ by [Your Name](https://github.com/yourname)
```

## Badge Reference

```yaml
badges:
  build_status:
    github_actions: "[![Build](https://img.shields.io/github/actions/workflow/status/OWNER/REPO/WORKFLOW.yml?branch=BRANCH)](URL)"
    travis: "[![Build Status](https://img.shields.io/travis/OWNER/REPO.svg)](URL)"
    circleci: "[![CircleCI](https://img.shields.io/circleci/build/github/OWNER/REPO)](URL)"

  package_version:
    npm: "[![npm](https://img.shields.io/npm/v/PACKAGE.svg)](URL)"
    pypi: "[![PyPI](https://img.shields.io/pypi/v/PACKAGE.svg)](URL)"
    gem: "[![Gem](https://img.shields.io/gem/v/PACKAGE.svg)](URL)"

  coverage:
    codecov: "[![codecov](https://img.shields.io/codecov/c/github/OWNER/REPO)](URL)"
    coveralls: "[![Coverage](https://img.shields.io/coveralls/github/OWNER/REPO)](URL)"

  license:
    mit: "[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)"
    apache: "[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)"
    gpl: "[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)"

  downloads:
    npm: "[![Downloads](https://img.shields.io/npm/dm/PACKAGE.svg)](URL)"
    pypi: "[![Downloads](https://img.shields.io/pypi/dm/PACKAGE.svg)](URL)"

  quality:
    codacy: "[![Codacy Badge](https://img.shields.io/codacy/grade/PROJECT_ID)](URL)"
    code_climate: "[![Maintainability](https://img.shields.io/codeclimate/maintainability/OWNER/REPO)](URL)"

  social:
    stars: "[![GitHub stars](https://img.shields.io/github/stars/OWNER/REPO)](URL)"
    forks: "[![GitHub forks](https://img.shields.io/github/forks/OWNER/REPO)](URL)"
    watchers: "[![GitHub watchers](https://img.shields.io/github/watchers/OWNER/REPO)](URL)"
```

## Section Guidelines

```yaml
essential_sections:
  title_and_badges:
    purpose: "Immediate project identification"
    elements:
      - "Project name (H1)"
      - "Key status badges"
      - "One-line description"

  features:
    purpose: "Highlight key capabilities"
    format: "Bullet list with checkmarks"
    length: "5-8 items maximum"

  quick_start:
    purpose: "Fastest path to running code"
    requirements:
      - "Copy-pasteable commands"
      - "Under 5 steps"
      - "Working example"

  installation:
    purpose: "Complete setup instructions"
    include:
      - "Prerequisites with versions"
      - "Multiple package managers"
      - "Platform-specific notes"

  usage:
    purpose: "Demonstrate core functionality"
    include:
      - "Basic example"
      - "Advanced configuration"
      - "Real-world use case"

  api_reference:
    purpose: "Complete function documentation"
    format:
      - "Function signature"
      - "Parameter table"
      - "Return type"
      - "Code example"

optional_sections:
  - "Architecture diagram"
  - "Benchmarks/Performance"
  - "FAQ"
  - "Roadmap"
  - "Security policy"
  - "Code of conduct"
```

## Writing Guidelines

```yaml
tone:
  - "Clear and concise"
  - "Action-oriented"
  - "Beginner-friendly"
  - "Scannable"

structure:
  headings: "Use H2 for main sections, H3 for subsections"
  lists: "Prefer bullet points over paragraphs"
  code: "Always include language identifier"
  tables: "Use for structured data (parameters, options)"

code_blocks:
  requirements:
    - "Always specify language"
    - "Include necessary imports"
    - "Show expected output when helpful"
    - "Use realistic values, not 'foo/bar'"

  example:
    good: |
      ```javascript
      import { Client } from 'my-package';

      const client = new Client({ apiKey: process.env.API_KEY });
      const result = await client.search('nodejs tutorials');
      console.log(result.items);
      ```
    bad: |
      ```
      const x = new X();
      x.foo();
      ```

anti_patterns:
  - "Wall of text without headings"
  - "Missing installation instructions"
  - "Outdated badges or broken links"
  - "Code examples that don't work"
  - "Assuming reader knowledge"
  - "Missing license information"
```

## Project Type Templates

```yaml
templates:
  library:
    sections:
      - "Title + Badges"
      - "Features"
      - "Installation"
      - "Quick Start"
      - "API Reference"
      - "Examples"
      - "Contributing"
      - "License"

  cli_tool:
    sections:
      - "Title + Badges"
      - "Features"
      - "Installation"
      - "Usage (with commands)"
      - "Configuration"
      - "Examples"
      - "Contributing"
      - "License"

  api_service:
    sections:
      - "Title + Badges"
      - "Features"
      - "Getting Started"
      - "Authentication"
      - "API Reference"
      - "Rate Limits"
      - "Error Handling"
      - "SDKs"
      - "Support"

  framework:
    sections:
      - "Title + Badges"
      - "Why This Framework"
      - "Features"
      - "Quick Start"
      - "Documentation"
      - "Examples"
      - "Ecosystem"
      - "Migration Guide"
      - "Contributing"
      - "License"
```

## Visual Elements

```yaml
diagrams:
  architecture: |
    ```
    ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
    │   Client    │────▶│   Server    │────▶│  Database   │
    └─────────────┘     └─────────────┘     └─────────────┘
    ```

  flow: |
    ```
    Input ──▶ Validate ──▶ Process ──▶ Output
                │
                ▼
              Error
    ```

tables:
  comparison: |
    | Feature | This Project | Alternative A | Alternative B |
    |---------|--------------|---------------|---------------|
    | Speed   | ⚡ Fast      | 🐢 Slow       | 🚀 Fastest    |
    | Size    | 📦 Small     | 📦 Medium     | 📦 Large      |

  features: |
    | Feature | Free | Pro | Enterprise |
    |---------|:----:|:---:|:----------:|
    | Basic   | ✅   | ✅  | ✅         |
    | Advanced| ❌   | ✅  | ✅         |
    | Support | ❌   | ❌  | ✅         |
```

## Checklist

```yaml
validation_checklist:
  structure:
    - "[ ] Title and description present"
    - "[ ] Badges are current and working"
    - "[ ] Installation instructions complete"
    - "[ ] Quick start works on fresh clone"
    - "[ ] API documentation accurate"
    - "[ ] License file present"

  quality:
    - "[ ] No broken links"
    - "[ ] Code examples tested and working"
    - "[ ] Screenshots/GIFs current"
    - "[ ] No typos or grammar issues"
    - "[ ] Consistent formatting"

  accessibility:
    - "[ ] Alt text for images"
    - "[ ] Proper heading hierarchy"
    - "[ ] Code blocks have language specified"
    - "[ ] Tables have headers"
```

## Лучшие практики

1. **Start with Quick Start** — пользователи хотят результат быстро
2. **Show, don't tell** — код важнее объяснений
3. **Keep it current** — обновляй при каждом релизе
4. **Test everything** — все примеры должны работать
5. **Use badges wisely** — только релевантные, рабочие
6. **Structure for scanning** — заголовки, списки, таблицы

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

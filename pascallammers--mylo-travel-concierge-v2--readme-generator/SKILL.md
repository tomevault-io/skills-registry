---
name: readme-generator
description: Auto-activates when user mentions README, project documentation, getting started guide, or creating project docs. Generates comprehensive README.md. Use when this capability is needed.
metadata:
  author: pascallammers
---

# README Generator

Generates professional, comprehensive README.md for any project.

## When This Activates

- User says: "create README", "generate README", "project documentation"
- New project initialization
- Missing or outdated README

## README Template

```markdown
# Project Name

Brief one-sentence description of what this project does.

## 🚀 Features

- Feature 1 - Brief description
- Feature 2 - Brief description  
- Feature 3 - Brief description

## 📦 Installation

### Prerequisites

- Node.js 18+ (or Python 3.11+, etc.)
- Database (PostgreSQL, MongoDB, etc.)
- Other requirements

### Quick Start

\`\`\`bash
# Clone repository
git clone https://github.com/username/project.git
cd project

# Install dependencies
npm install  # or: bun install, pip install -r requirements.txt

# Set up environment variables
cp .env.example .env
# Edit .env with your configuration

# Run database migrations
npm run db:migrate

# Start development server
npm run dev
\`\`\`

Visit `http://localhost:3000` to see the app.

## 🛠️ Usage

### Basic Example

\`\`\`javascript
import { someFunction } from 'project';

const result = someFunction({ param: 'value' });
console.log(result);
\`\`\`

### Advanced Example

\`\`\`javascript
// More complex usage example
\`\`\`

## 📖 API Reference

### Main Functions

#### \`functionName(param1, param2)\`

Description of what the function does.

**Parameters:**
- \`param1\` (string): Description
- \`param2\` (number): Description

**Returns:** Description of return value

**Example:**
\`\`\`javascript
const result = functionName('value', 42);
\`\`\`

## 🏗️ Project Structure

\`\`\`
project/
├── src/               # Source code
│   ├── components/    # React components
│   ├── lib/          # Utility libraries
│   └── app/          # Main application
├── tests/            # Test files
├── docs/             # Documentation
└── scripts/          # Build/deployment scripts
\`\`\`

## 🧪 Testing

\`\`\`bash
# Run all tests
npm test

# Run specific test file
npm test path/to/test.test.ts

# Run with coverage
npm run test:coverage

# Run in watch mode
npm run test:watch
\`\`\`

## 🚢 Deployment

### Production Build

\`\`\`bash
npm run build
npm start
\`\`\`

### Docker

\`\`\`bash
docker build -t project-name .
docker run -p 3000:3000 project-name
\`\`\`

### Deploy to Vercel/Netlify/etc.

\`\`\`bash
# Platform-specific deployment commands
\`\`\`

## ⚙️ Configuration

### Environment Variables

Create a \`.env\` file in the root directory:

\`\`\`env
DATABASE_URL=postgresql://user:pass@localhost:5432/dbname
API_KEY=your_api_key_here
NODE_ENV=development
\`\`\`

See \`.env.example\` for all available options.

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create your feature branch (\`git checkout -b feature/amazing-feature\`)
3. Commit your changes (\`git commit -m 'feat: add amazing feature'\`)
4. Push to the branch (\`git push origin feature/amazing-feature\`)
5. Open a Pull Request

Please ensure:
- All tests pass
- Code follows project style guide
- Commit messages follow [Conventional Commits](https://www.conventionalcommits.org/)

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👥 Authors

- **Your Name** - [GitHub](https://github.com/username)

## 🙏 Acknowledgments

- Hat tip to anyone whose code was used
- Inspiration sources
- Libraries/tools used

## 📞 Support

- 📧 Email: support@example.com
- 💬 Discord: [Join our server](https://discord.gg/xyz)
- 🐛 Issues: [GitHub Issues](https://github.com/username/project/issues)

## 📚 Additional Resources

- [Documentation](https://docs.example.com)
- [Changelog](CHANGELOG.md)
- [API Reference](https://api.example.com)
- [Contributing Guide](CONTRIBUTING.md)

---

Made with ❤️ by [Your Name](https://github.com/username)
```

## Auto-Detection

Analyze project to auto-fill:

1. **Language/Framework:**
   - package.json → Node.js/TypeScript
   - requirements.txt → Python
   - Cargo.toml → Rust
   - go.mod → Go

2. **Scripts:**
   - Extract from package.json scripts
   - Detect test commands
   - Find build commands

3. **Dependencies:**
   - List major dependencies
   - Mention notable libraries

4. **Project Structure:**
   - Scan directories
   - Identify patterns (Next.js, React, etc.)

## Badges

Add relevant badges:

```markdown
![Build Status](https://github.com/user/repo/workflows/CI/badge.svg)
![Coverage](https://codecov.io/gh/user/repo/branch/main/graph/badge.svg)
![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Version](https://img.shields.io/npm/v/package-name.svg)
```

## Best Practices

✅ **DO:**
- Keep it concise (aim for 1-2 screen lengths)
- Include working examples
- Add badges for status/coverage
- Update regularly with changes
- Include troubleshooting section

❌ **DON'T:**
- Write a novel (keep it scannable)
- Use outdated examples
- Forget installation steps
- Skip configuration details
- Miss contributor guidelines

**Generate README, present to user, write file with approval.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pascallammers) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

---
name: quick-start-guide
description: Эксперт по quick start гайдам. Используй для создания быстрых руководств, getting started документации и onboarding материалов. Use when this capability is needed.
metadata:
  author: neversight
---

# Quick Start Guide Creator

Expert in creating effective onboarding documentation that helps users achieve working implementations in 5-15 minutes.

## Core Principles

### Time-to-Value Optimization
- Lead with impressive features, not setup
- Deliver immediate value through early wins
- Defer advanced concepts to later documentation
- Target completion in 5-15 minutes

### Progressive Disclosure
- Start with clear prerequisites
- Use numbered steps
- Build complexity gradually
- Link to deeper documentation

## Quick Start Template

```markdown
# Quick Start: [Product Name]

Get [product] running in **5 minutes**. By the end, you'll have [specific outcome].

## Prerequisites

Before starting, ensure you have:
- [ ] Node.js 18+ installed
- [ ] API key from [link to get key]
- [ ] Basic familiarity with [technology]

## Step 1: Install

```bash
npm install @company/product
```

**Expected output:**
```
added 42 packages in 3s
```

## Step 2: Configure

Create `config.js` in your project root:

```javascript
// config.js
module.exports = {
  apiKey: process.env.API_KEY,
  environment: 'development'
};
```

## Step 3: Run Your First [Action]

```javascript
// index.js
const product = require('@company/product');
const config = require('./config');

const client = product.init(config);
const result = await client.doSomething();
console.log(result);
```

Run it:
```bash
API_KEY=your_key node index.js
```

**Expected output:**
```json
{
  "status": "success",
  "message": "Hello from Product!"
}
```

## Verify It Works

You should see:
- ✅ No errors in console
- ✅ Success response with status
- ✅ [Specific verification]

## Troubleshooting

### "API key invalid" error
- Verify your key at [dashboard link]
- Ensure no extra spaces in the key
- Check key permissions

### "Module not found" error
- Run `npm install` again
- Verify Node.js version: `node --version`

## Next Steps

Now that you have [product] running:
- 📖 [Full Configuration Guide](link)
- 🔧 [API Reference](link)
- 💡 [Example Projects](link)
- 💬 [Community Discord](link)
```

## Writing Standards

```yaml
writing_guidelines:
  voice: "Active voice, imperative mood"
  reading_level: "8th grade"
  sentence_length: "Under 20 words"
  address: "Direct 'you' to reader"

  examples:
    good: "Run this command to install dependencies."
    bad: "Dependencies should be installed by running..."

code_examples:
  requirements:
    - "Complete and runnable"
    - "Show actual file paths"
    - "Include all imports"
    - "Use realistic values"

  structure:
    - "File name as comment"
    - "Necessary imports first"
    - "Configuration second"
    - "Action code last"

anti_patterns:
  avoid:
    - "Information overload"
    - "Vague instructions like 'configure your environment'"
    - "Missing file locations"
    - "Assuming knowledge"
    - "Multiple options without recommendation"
```

## Validation-Driven Structure

```yaml
validation_points:
  after_each_step:
    - "Expected output with example"
    - "What success looks like"
    - "Common error and fix"

  success_criteria:
    - "80% of users complete successfully"
    - "Working output on first attempt"
    - "Under 15 minutes total"

verification_checklist:
  - "[ ] All code blocks tested"
  - "[ ] Expected outputs accurate"
  - "[ ] Links working"
  - "[ ] Prerequisites complete"
  - "[ ] Troubleshooting covers common issues"
```

## Platform Adaptations

```yaml
platform_variations:
  installation:
    macos: "brew install product"
    linux: "apt-get install product"
    windows: "choco install product"

  paths:
    macos: "~/Library/Application Support/Product"
    linux: "~/.config/product"
    windows: "%APPDATA%\\Product"

  commands:
    unix: "export API_KEY=your_key"
    windows: "set API_KEY=your_key"
```

## Лучшие практики

1. **Test every step** — каждый шаг должен быть проверен
2. **Show expected output** — пользователь должен знать что увидит
3. **One path to success** — не давайте выбор, давайте рекомендацию
4. **Troubleshooting section** — покройте частые ошибки
5. **Clear next steps** — направьте к следующим ресурсам
6. **Keep it short** — 5-15 минут максимум

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

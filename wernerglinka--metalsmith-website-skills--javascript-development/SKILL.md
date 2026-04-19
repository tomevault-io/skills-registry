---
name: javascript-development
description: JavaScript development patterns for Metalsmith plugins, browser scripts, and Node.js tooling. Triggers when writing JavaScript code, creating Metalsmith plugins, browser functionality, Nunjucks templates, or Node.js build scripts. Enforces functional programming patterns, ES6+ features, JSDoc documentation, dual ESM/CJS module support, and Mocha testing. No TypeScript. Use when this capability is needed.
metadata:
  author: wernerglinka
---

# JavaScript Development Skill

Prescriptive guidance for modern JavaScript development following functional patterns, ES6+ features, clean architecture, and modern tooling without TypeScript.

## Core Principles

1. **Functional over imperative** - Use pure functions, avoid mutation, prefer composition
2. **Immutability by default** - Use `const` unless reassignment is required, then `let`
3. **Explicit over implicit** - Return values explicitly, no side effects, clear data flow
4. **Single responsibility** - Each function does one thing well
5. **DRY (Don't Repeat Yourself)** - Encapsulate repetitive logic in functions
6. **Native first** - Use Node.js and browser APIs before reaching for npm packages
7. **No TypeScript** - Use JSDoc for type hints that IDEs understand

## ES6+ Features

### Variable Declarations

```javascript
// Always use const by default
const options = { pattern: '**/*.html' };
const files = Object.keys(metalsmith.files);

// Use let only when reassignment is needed
let count = 0;
for (const file of files) {
  if (matchesPattern(file)) {
    count += 1;
  }
}

// Never use var
```

### Destructuring

```javascript
// Object destructuring in function parameters
function processFile({ contents, mode, stats }, options) {
  const { pattern, ignore } = options;
  // ...
}

// Array destructuring
const [first, second, ...rest] = items;

// Destructuring with defaults
const { pattern = '**/*.html', ignore = [] } = options;

// Nested destructuring
const { fuseOptions: { threshold = 0.3 } } = config;
```

### Template Literals

```javascript
// Use template literals for string interpolation
const message = `Processing ${files.length} files with pattern: ${pattern}`;

// Multi-line strings
const html = `
  <div class="card">
    <h2>${title}</h2>
    <p>${description}</p>
  </div>
`;

// Tagged templates for complex cases
const query = sql`SELECT * FROM users WHERE id = ${userId}`;
```

### Arrow Functions

```javascript
// Use arrow functions for short callbacks and pure functions
const doubled = numbers.map((n) => n * 2);

// Use regular functions for methods that need 'this' or for named exports
export function processFiles(files, options) {
  // ...
}

// Arrow function for inline handlers
input.addEventListener('input', (event) => {
  handleInput(event.target.value);
});
```

### Default Parameters

```javascript
// Use default parameters instead of conditional checks
function createIndex(files, options = {}) {
  const { pattern = '**/*.html', threshold = 0.3 } = options;
  // ...
}

// Avoid this pattern
function createIndex(files, options) {
  options = options || {};  // Don't do this
  const pattern = options.pattern || '**/*.html';  // Or this
}
```

### Spread and Rest Operators

```javascript
// Spread for immutable updates
const updated = { ...original, newProp: value };
const combined = [...array1, ...array2];

// Rest for gathering remaining arguments
function log(message, ...args) {
  console.log(message, ...args);
}

// Rest in destructuring
const { pattern, ...otherOptions } = options;
```

## Strict Equality and Defensive Coding

### Always Use Strict Equality

```javascript
// Always use === and !==
if (value === null) { }
if (type !== 'undefined') { }
if (count === 0) { }

// Never use == or != (type coercion causes bugs)
if (value == null) { }  // Don't do this
if (count == '0') { }   // Or this
```

### Optional Chaining (?.)

```javascript
// Safe property access
const title = file?.metadata?.title;
const firstTag = post?.tags?.[0];
const result = processor?.process?.(data);

// Instead of
const title = file && file.metadata && file.metadata.title;
```

### Nullish Coalescing (??)

```javascript
// Use ?? for defaults when null/undefined (not falsy)
const port = config.port ?? 3000;        // Only if null/undefined
const name = user.name ?? 'Anonymous';

// Use || only when you want to replace all falsy values
const count = input || 0;  // Replaces '', 0, false, null, undefined

// Important difference:
const value1 = 0 ?? 10;    // Returns 0 (0 is not null/undefined)
const value2 = 0 || 10;    // Returns 10 (0 is falsy)
const value3 = '' ?? 'default';  // Returns '' (empty string is not null/undefined)
const value4 = '' || 'default';  // Returns 'default' (empty string is falsy)
```

### Defensive Type Checks

```javascript
// Check types explicitly when needed
function processFile(file, options) {
  if (typeof file !== 'object' || file === null) {
    throw new Error('metalsmith-plugin: file must be an object');
  }
  
  if (!Array.isArray(options.patterns)) {
    throw new Error('metalsmith-plugin: patterns must be an array');
  }
}
```

## Async/Await Patterns

### Basic Async Functions

```javascript
/**
 * Read and parse a JSON file
 * @param {string} filePath - Path to JSON file
 * @returns {Promise<Object>} Parsed JSON content
 */
async function readJsonFile(filePath) {
  const content = await readFile(filePath, 'utf8');
  return JSON.parse(content);
}
```

### Error Handling with Async/Await

Only catch at boundaries - external calls where you can add context or recover:

```javascript
/**
 * Fetch data from external API
 * This is a boundary - external call can fail for reasons outside our control
 * @param {string} url - URL to fetch
 * @returns {Promise<Object>} Response data
 */
async function fetchData(url) {
  const response = await fetch(url);
  
  if (!response.ok) {
    throw new Error(`HTTP ${response.status} from ${url}: ${response.statusText}`);
  }
  
  return response.json();
}

// No try/catch in trusted async code - let errors bubble
async function processAllEntries(entries, options) {
  const results = await Promise.all(
    entries.map((entry) => processEntry(entry, options))
  );
  return results.filter(Boolean);
}
```

### Parallel Async Operations

```javascript
// Process multiple items in parallel
async function processAllFiles(files, options) {
  const results = await Promise.all(
    files.map((file) => processFile(file, options))
  );
  return results.filter(Boolean);
}

// With error handling for individual items
async function processAllFilesSafe(files, options) {
  const results = await Promise.allSettled(
    files.map((file) => processFile(file, options))
  );
  
  return results
    .filter((result) => result.status === 'fulfilled')
    .map((result) => result.value);
}
```

### Sequential Async Operations

```javascript
// When order matters or you need to avoid overwhelming a resource
async function processSequentially(items, processor) {
  const results = [];
  
  for (const item of items) {
    const result = await processor(item);
    results.push(result);
  }
  
  return results;
}

// Using reduce for sequential async
async function processSequentiallyAlt(items, processor) {
  return items.reduce(async (accPromise, item) => {
    const acc = await accPromise;
    const result = await processor(item);
    return [...acc, result];
  }, Promise.resolve([]));
}
```

### Async in Metalsmith Plugins

```javascript
/**
 * Async-aware Metalsmith plugin
 * @param {Options} options - Plugin options
 * @returns {import('metalsmith').Plugin} Metalsmith plugin function
 */
export default function asyncPlugin(options = {}) {
  const config = deepMerge(defaultOptions, options);

  return async function (files, metalsmith) {
    const debug = metalsmith.debug('metalsmith-async-plugin');
    debug('Starting async processing');

    const filesToProcess = metalsmith.match(config.pattern, Object.keys(files));
    
    // Process files in parallel
    await Promise.all(
      filesToProcess.map(async (filename) => {
        const file = files[filename];
        const result = await processFileAsync(file, config);
        files[filename] = { ...file, ...result };
      })
    );

    debug('Async processing complete');
  };
}
```

## Native Methods Over Dependencies

Always prefer built-in APIs. Every dependency is a liability.

### Node.js Native Modules

| Instead of | Use |
|------------|-----|
| `fs-extra` | `node:fs/promises` |
| `mkdirp` | `fs.mkdir(path, { recursive: true })` |
| `rimraf` | `fs.rm(path, { recursive: true, force: true })` |
| `axios`, `node-fetch` | `fetch` (native since Node 18) |
| `glob` | `fs.glob` (Node 22+) or `metalsmith.match()` |
| `lodash.clonedeep` | `structuredClone()` |
| `lodash.merge` | Custom `deepMerge` (see Configuration Pattern) |
| `lodash.get` | Optional chaining `obj?.nested?.prop` |
| `lodash.defaultsDeep` | Nullish coalescing `??` with destructuring |
| `uuid` | `crypto.randomUUID()` |
| `query-string` | `URLSearchParams` |
| `promisify` wrappers | `node:fs/promises` or `node:util` `promisify` |
| `chalk` for simple colors | ANSI codes or keep output plain |
| `debug` package | `metalsmith.debug()` in plugins |
| `minimatch` | `metalsmith.match()` in plugins |

### Browser Native APIs

| Instead of | Use |
|------------|-----|
| jQuery | `document.querySelector`, `addEventListener` |
| Axios | `fetch` |
| Lodash array methods | `Array.prototype` methods |
| Moment.js | `Intl.DateTimeFormat`, `Date` methods |
| classnames | Template literals |

### When Dependencies Are Appropriate

Add a dependency only when:
- Native equivalent doesn't exist or is significantly worse
- The package solves a complex, well-tested problem (e.g., `cheerio` for HTML parsing, `fuse.js` for fuzzy search)
- Writing it yourself would be error-prone or time-consuming
- The package is well-maintained with minimal transitive dependencies

## Metalsmith Plugin Pattern

Every Metalsmith plugin uses the two-phase factory pattern:

```javascript
/**
 * Plugin options
 * @typedef {Object} Options
 * @property {string|string[]} [pattern] - Files to process
 * @property {string} [outputPath] - Output destination
 */

/**
 * Brief description of what the plugin does.
 *
 * @param {Options} options - Plugin options
 * @returns {import('metalsmith').Plugin} Metalsmith plugin function
 */
export default function pluginName(options = {}) {
  const config = deepMerge(defaultOptions, options);

  const metalsmithPlugin = function (files, metalsmith, done) {
    const debug = metalsmith.debug('metalsmith-plugin-name');
    debug('Starting with options:', config);

    try {
      // Plugin logic here
      done();
    } catch (error) {
      debug('Plugin failed:', error);
      done(error);
    }
  };

  Object.defineProperty(metalsmithPlugin, 'name', {
    value: 'pluginNamePlugin',
    configurable: true,
  });

  return metalsmithPlugin;
}
```

### Metalsmith Native Methods

Always use Metalsmith's built-in methods over external packages:

| Instead of | Use |
|------------|-----|
| `debug` package | `metalsmith.debug('namespace')` |
| `minimatch` | `metalsmith.match(pattern, files)` |
| `process.env` | `metalsmith.env('VAR_NAME')` |
| `path.join` for metalsmith paths | `metalsmith.path('subdir', 'file')` |

## Configuration Pattern

Separate configuration into its own module with deep merge and normalization:

```javascript
/**
 * Default plugin options
 * @type {Object}
 */
export const defaultOptions = {
  pattern: '**/*.html',
  ignore: [],
  outputPath: 'output.json',
};

/**
 * Deep merge configuration objects without mutation
 * @param {Object} target - Base configuration
 * @param {Object} source - Override configuration
 * @returns {Object} Merged configuration
 */
export const deepMerge = (target, source) =>
  Object.keys(source).reduce(
    (acc, key) => ({
      ...acc,
      [key]:
        source[key]?.constructor === Object
          ? deepMerge(target[key] ?? {}, source[key])
          : source[key],
    }),
    { ...target }
  );

/**
 * Convert value to array
 * @param {*} value - Value to normalize
 * @returns {Array} Array version of value
 */
export function normalizeToArray(value) {
  if (typeof value === 'string') {
    return [value];
  }
  if (Array.isArray(value)) {
    return value;
  }
  return [];
}
```

## JSDoc Style

Use JSDoc for all exported functions and complex types:

```javascript
/**
 * @typedef {Object} SearchEntry
 * @property {string} id - Unique identifier
 * @property {string} url - Page URL
 * @property {string} title - Page title
 * @property {string} content - Text content
 */

/**
 * Process a single file and extract search data.
 * Uses Cheerio for HTML parsing to ensure accurate content extraction.
 *
 * @param {string} filename - Path to file being processed
 * @param {Object} fileData - Metalsmith file object with contents and metadata
 * @param {Object} options - Normalized plugin options
 * @returns {SearchEntry|null} Search entry or null if file should be skipped
 */
export function processFile(filename, fileData, options) {
  // Implementation
}
```

### JSDoc Rules

- `@typedef` for complex option objects and data structures
- `@param` with type, name, and description for every parameter
- `@returns` with type and description
- Brief description as first line, details after blank line
- Use `{import('metalsmith').Plugin}` for Metalsmith types
- No abbreviations: use `error` not `err`, `options` not `opts`

## Functional Patterns

### Prefer Pure Functions

```javascript
// Good: Pure function, no side effects
function filterIgnoredFiles(matchedFiles, ignoredFiles) {
  return matchedFiles.filter((filename) => !ignoredFiles.includes(filename));
}

// Bad: Mutates input
function filterIgnoredFiles(matchedFiles, ignoredFiles) {
  ignoredFiles.forEach((file) => {
    const index = matchedFiles.indexOf(file);
    if (index > -1) matchedFiles.splice(index, 1);
  });
  return matchedFiles;
}
```

### Use Reduce for Transformations

```javascript
// Good: Functional transformation with immutable accumulator
const result = items.reduce(
  (acc, item) => ({
    ...acc,
    [item.id]: processItem(item),
  }),
  {}
);

// Alternative with Map for better performance on large datasets
const result = new Map(
  items.map((item) => [item.id, processItem(item)])
);
```

### Single-Purpose Functions (DRY)

```javascript
// Good: Each function does one thing, logic is not repeated
function hasIgnorePatterns(ignore) {
  return ignore.length > 0;
}

function getMatchedFiles(pattern, files, metalsmith) {
  return metalsmith.match(pattern, Object.keys(files));
}

function filterIgnored(matched, ignored) {
  return matched.filter((file) => !ignored.includes(file));
}

// Compose single-purpose functions
function getFilesToProcess(files, options, metalsmith) {
  const matched = getMatchedFiles(options.pattern, files, metalsmith);
  
  if (!hasIgnorePatterns(options.ignore)) {
    return matched;
  }
  
  const ignored = getMatchedFiles(options.ignore, files, metalsmith);
  return filterIgnored(matched, ignored);
}
```

### Early Returns Over Nested Conditionals

```javascript
// Good: Early returns
function processFile(file, options) {
  if (!file) {
    return null;
  }
  
  if (!matchesPattern(file, options.pattern)) {
    return null;
  }
  
  return extractContent(file);
}

// Bad: Deep nesting
function processFile(file, options) {
  if (file) {
    if (matchesPattern(file, options.pattern)) {
      return extractContent(file);
    }
  }
  return null;
}
```

## Linting and Formatting

Use ESLint and Prettier to enforce consistent style and catch errors.

### ESLint Configuration (eslint.config.js)

```javascript
import eslintPluginPrettier from 'eslint-plugin-prettier';
import eslintConfigPrettier from 'eslint-config-prettier';

export default [
  {
    ignores: ['lib/**', 'node_modules/**', 'coverage/**'],
  },
  {
    files: ['**/*.js'],
    languageOptions: {
      ecmaVersion: 2022,
      sourceType: 'module',
    },
    plugins: {
      prettier: eslintPluginPrettier,
    },
    rules: {
      ...eslintConfigPrettier.rules,
      'prettier/prettier': 'error',
      'no-var': 'error',
      'prefer-const': 'error',
      'eqeqeq': ['error', 'always'],
      'no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
    },
  },
];
```

### Prettier Configuration (prettier.config.js)

```javascript
export default {
  semi: true,
  singleQuote: true,
  trailingComma: 'es5',
  printWidth: 100,
  tabWidth: 2,
};
```

### Key Rules Enforced

- `no-var` - Prevents use of `var`
- `prefer-const` - Requires `const` when variable isn't reassigned
- `eqeqeq` - Requires `===` and `!==`
- `no-unused-vars` - Catches dead code

## Module Structure

### Directory Layout

```
plugin-name/
├── src/
│   ├── index.js           # Main entry, exports plugin factory
│   ├── utils/
│   │   └── config.js      # Configuration, defaults, deep merge
│   └── processors/
│       └── file-processor.js  # Core processing logic
├── test/
│   ├── index.test.js      # ESM tests
│   ├── cjs.test.cjs       # CJS compatibility tests
│   └── fixtures/          # Test fixtures
├── lib/                   # Built output (generated)
├── package.json
├── eslint.config.js
└── prettier.config.js
```

### Dual ESM/CJS Support

Use microbundle for building both module formats:

**package.json:**
```json
{
  "type": "module",
  "main": "./lib/index.cjs",
  "module": "./lib/index.js",
  "exports": {
    "import": "./lib/index.js",
    "require": "./lib/index.cjs",
    "default": "./lib/index.js"
  },
  "scripts": {
    "build": "microbundle --entry src/index.js --output lib/index.js --target node -f esm,cjs --strict --generateTypes=false",
    "lint": "eslint --fix .",
    "lint:check": "eslint .",
    "format": "prettier --write \"**/*.{js,json,md}\"",
    "format:check": "prettier --check \"**/*.{js,json,md}\""
  },
  "engines": {
    "node": ">= 18.0.0"
  }
}
```

## Error Handling

**Catch at boundaries, let errors bubble in trusted code.**

Over-defensive code with nested try/catch blocks obscures the real error source, hides bugs that should crash, and adds noise. If a trusted internal function fails, the stack trace is your best debugging tool.

### Where to Catch

**Entry points and boundaries:**
```javascript
// Plugin entry point - this IS a boundary
const metalsmithPlugin = function (files, metalsmith, done) {
  const debug = metalsmith.debug('metalsmith-plugin-name');

  try {
    const result = processFiles(files, config);
    done();
  } catch (error) {
    debug('Plugin failed:', error);
    done(error);  // Propagate to Metalsmith
  }
};
```

**External calls (network, file system, user input):**
```javascript
// External call - can fail for reasons outside your control
async function fetchConfig(url) {
  const response = await fetch(url);
  
  if (!response.ok) {
    throw new Error(`HTTP ${response.status}: ${response.statusText}`);
  }
  
  return response.json();
}
```

**When you can actually recover:**
```javascript
// Recovery is possible - try cache on network failure
async function getDataWithFallback(url, cacheKey) {
  try {
    return await fetchData(url);
  } catch (error) {
    const cached = await cache.get(cacheKey);
    if (cached) {
      return cached;
    }
    throw error;  // Re-throw if no recovery possible
  }
}
```

### Where NOT to Catch

**Trusted internal functions - let them fail:**
```javascript
// Good: No try/catch, errors bubble up naturally
function deepMerge(target, source) {
  return Object.keys(source).reduce(
    (acc, key) => ({
      ...acc,
      [key]:
        source[key]?.constructor === Object
          ? deepMerge(target[key] ?? {}, source[key])
          : source[key],
    }),
    { ...target }
  );
}

// Good: Validation throws, caller decides how to handle
function validateOptions(options) {
  if (!options.pattern) {
    throw new Error('metalsmith-plugin-name: pattern option is required');
  }
}

// Good: Pure transformation, no catching needed
function filterIgnoredFiles(matchedFiles, ignoredFiles) {
  return matchedFiles.filter((filename) => !ignoredFiles.includes(filename));
}
```

**Bad: Over-defensive internal code:**
```javascript
// Bad: Catching in trusted code obscures bugs
function deepMerge(target, source) {
  try {
    return Object.keys(source).reduce(/* ... */);
  } catch (error) {
    console.error('Merge failed:', error);  // Now you've hidden the real problem
    return target;  // Silent failure - bugs will surface elsewhere
  }
}

// Bad: Wrapping every call
function processFiles(files, options) {
  try {
    const validated = validateOptions(options);  // Unnecessary
    try {
      const merged = deepMerge(defaults, validated);  // Unnecessary
      try {
        return transform(files, merged);  // Unnecessary
      } catch (e) { /* ... */ }
    } catch (e) { /* ... */ }
  } catch (e) { /* ... */ }
}
```

### The Rule

Ask: "Can I recover here, or am I just re-throwing with less information?"

If the answer is "just re-throwing," remove the try/catch and let the error bubble to a boundary that can handle it properly.

## Testing with Mocha

### Unit Tests

```javascript
import assert from 'node:assert';
import { describe, it } from 'mocha';
import { deepMerge, normalizeToArray } from '../src/utils/config.js';

describe('config utilities', function () {
  describe('deepMerge', function () {
    it('should merge nested objects immutably', function () {
      const target = { a: { b: 1 } };
      const source = { a: { c: 2 } };
      const result = deepMerge(target, source);
      
      assert.deepStrictEqual(result, { a: { b: 1, c: 2 } });
      assert.deepStrictEqual(target, { a: { b: 1 } }); // Original unchanged
    });
  });

  describe('normalizeToArray', function () {
    it('should wrap string in array', function () {
      assert.deepStrictEqual(normalizeToArray('*.html'), ['*.html']);
    });

    it('should return array as-is', function () {
      const input = ['*.html', '*.md'];
      assert.strictEqual(normalizeToArray(input), input);
    });

    it('should return empty array for invalid input', function () {
      assert.deepStrictEqual(normalizeToArray(null), []);
      assert.deepStrictEqual(normalizeToArray(undefined), []);
    });
  });
});
```

### Integration Tests

```javascript
import assert from 'node:assert';
import { describe, it } from 'mocha';
import Metalsmith from 'metalsmith';
import plugin from '../lib/index.js';

describe('metalsmith-plugin integration', function () {
  it('should process files matching pattern', function (done) {
    Metalsmith('test/fixtures/basic')
      .use(plugin({ pattern: '**/*.html' }))
      .build(function (error, files) {
        assert.strictEqual(error, null);
        assert.ok(files['output.json']);
        done();
      });
  });

  it('should handle empty file set', function (done) {
    Metalsmith('test/fixtures/empty')
      .use(plugin())
      .build(function (error) {
        assert.strictEqual(error, null);
        done();
      });
  });
});
```

### Test Both Module Formats

```javascript
// test/cjs.test.cjs - CommonJS compatibility
const assert = require('node:assert');
const { describe, it } = require('mocha');
const plugin = require('../lib/index.cjs');

describe('CommonJS compatibility', function () {
  it('should export plugin function', function () {
    assert.strictEqual(typeof plugin, 'function');
  });

  it('should accept options', function () {
    const instance = plugin({ pattern: '**/*.md' });
    assert.strictEqual(typeof instance, 'function');
  });
});
```

## Browser JavaScript

For browser scripts, use vanilla JS with ES modules:

```javascript
/**
 * Initialize search functionality
 * @param {Object} options - Configuration options
 * @param {string} options.inputSelector - Search input selector
 * @param {string} options.resultsSelector - Results container selector
 */
export function initSearch(options) {
  const input = document.querySelector(options.inputSelector);
  const results = document.querySelector(options.resultsSelector);

  if (!input || !results) {
    console.warn('Search elements not found');
    return;
  }

  input.addEventListener('input', handleSearchInput(results));
}

/**
 * Create search input handler (curried function)
 * @param {HTMLElement} resultsContainer - Results container element
 * @returns {Function} Event handler function
 */
function handleSearchInput(resultsContainer) {
  return (event) => {
    const query = event.target.value.trim();
    
    if (query.length < 2) {
      resultsContainer.innerHTML = '';
      return;
    }

    performSearch(query).then((results) => {
      renderResults(results, resultsContainer);
    });
  };
}
```

## Nunjucks Patterns

### Macro for Reusable Components

```nunjucks
{# components/button.njk #}
{% macro button(text, url, variant = 'primary') %}
<a href="{{ url }}" class="button button--{{ variant }}">
  {{ text }}
</a>
{% endmacro %}
```

### Custom Filters

```javascript
// In Metalsmith build - register filters in layouts config
.use(layouts({
  engineOptions: {
    filters: {
      /**
       * Format date for display
       * @param {string} dateString - ISO date string
       * @returns {string} Formatted date
       */
      formatDate(dateString) {
        return new Date(dateString).toLocaleDateString('en-US', {
          year: 'numeric',
          month: 'long',
          day: 'numeric',
        });
      },
    },
  },
}))
```

## Naming Conventions

| Element | Convention | Example |
|---------|------------|---------|
| Variables | camelCase, descriptive | `matchedFiles`, `searchResults` |
| Functions | camelCase, verb prefix | `getFiles`, `createIndex`, `processEntry` |
| Constants | UPPER_SNAKE_CASE | `DEFAULT_PATTERN`, `MAX_RESULTS` |
| Files | kebab-case | `file-processor.js`, `config.js` |
| Classes (rare) | PascalCase | `SearchIndex` |

**No abbreviations**: `error` not `err`, `options` not `opts`, `config` not `cfg`, `result` not `res`

## Code Style

### Imports

```javascript
// Node built-ins first (with node: prefix)
import { readFile, writeFile } from 'node:fs/promises';
import path from 'node:path';

// External dependencies
import Metalsmith from 'metalsmith';

// Internal modules (relative)
import { deepMerge, defaultOptions } from './utils/config.js';
import { processFile } from './processors/file-processor.js';
```

### Exports

```javascript
// Named exports for utilities
export { deepMerge, normalizeToArray, validateOptions };

// Default export for main plugin function
export default function pluginName(options = {}) {
  // ...
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wernerglinka) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

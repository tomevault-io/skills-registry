---
name: testing
description: Writing and running tests for SenangStart Icons Use when this capability is needed.
metadata:
  author: bookklik-technologies
---

# Testing Skill

This skill covers testing practices for SenangStart Icons using Vitest.

## Test Setup

**Configuration:** `vitest.config.js`

```javascript
export default defineConfig({
  test: {
    globals: true,           // describe, it, expect available globally
    environment: 'node',     // Node.js environment
    include: ['tests/**/*.test.js'],
    coverage: {
      provider: 'v8',
      reporter: ['text', 'html'],
      include: ['src/**/*.js', 'scripts/**/*.js'],
      exclude: ['src/svg/**']
    }
  }
});
```

## Test Files Overview

| File | Tests |
|------|-------|
| `ss-icon.test.js` | Web Component rendering |
| `ss-loader.test.js` | Class-based icon injection |
| `build-svgs.test.js` | SVG generation |
| `build-css.test.js` | CSS generation |
| `build-icon-docs.test.js` | Documentation generation |
| `build-pipeline.test.js` | Full build integration |

## Writing Tests

### Testing Build Scripts

Mock the file system to avoid actual file operations:

```javascript
import { describe, it, expect, vi, beforeEach } from 'vitest';
import fs from 'fs';

vi.mock('fs');

describe('build-svgs', () => {
  beforeEach(() => {
    vi.clearAllMocks();
    
    // Mock icons.json content
    fs.readFileSync.mockReturnValue(JSON.stringify([
      { name: 'Check', slug: 'check', src: 'M5 13l4 4L19 7', tags: [] }
    ]));
    
    fs.existsSync.mockReturnValue(true);
    fs.writeFileSync.mockImplementation(() => {});
  });

  it('should generate SVG files', () => {
    require('../scripts/build-svgs.js');
    
    expect(fs.writeFileSync).toHaveBeenCalledWith(
      expect.stringContaining('check.svg'),
      expect.stringContaining('<svg')
    );
  });
});
```

### Testing Web Component (with jsdom)

```javascript
import { describe, it, expect, beforeEach } from 'vitest';
import { JSDOM } from 'jsdom';

describe('SSIcon', () => {
  let document, window;

  beforeEach(() => {
    const dom = new JSDOM('<!DOCTYPE html><html><body></body></html>', {
      runScripts: 'dangerously'
    });
    window = dom.window;
    document = window.document;
    
    // Define custom element in jsdom context
    // ...
  });

  it('should render icon SVG', () => {
    const icon = document.createElement('ss-icon');
    icon.setAttribute('icon', 'check');
    document.body.appendChild(icon);
    
    expect(icon.shadowRoot.innerHTML).toContain('<svg');
  });
});
```

### Testing CSS Generation

```javascript
describe('build-css', () => {
  it('should generate base .ss class', () => {
    require('../scripts/build-css.js');
    
    const cssContent = fs.writeFileSync.mock.calls[0][1];
    expect(cssContent).toContain('.ss {');
    expect(cssContent).toContain('mask-size: contain');
  });

  it('should generate icon classes with mask-image', () => {
    require('../scripts/build-css.js');
    
    const cssContent = fs.writeFileSync.mock.calls[0][1];
    expect(cssContent).toContain('.ss-check {');
    expect(cssContent).toContain('mask-image: url("data:image/svg+xml');
  });
});
```

## Running Tests

### All Tests
```bash
npm test
```

### Watch Mode
```bash
npm run test:watch
```

### With Coverage
```bash
npm run test:coverage
```

### Specific Test File
```bash
npx vitest run tests/ss-icon.test.js
```

## Test Patterns

### Mocking Child Process

```javascript
import { execSync } from 'child_process';

vi.mock('child_process', () => ({
  execSync: vi.fn()
}));

it('should run webpack', () => {
  require('../scripts/build.js');
  expect(execSync).toHaveBeenCalledWith(
    'npx webpack --mode production',
    expect.any(Object)
  );
});
```

### Testing Error Handling

```javascript
it('should handle missing icon gracefully', () => {
  const icon = document.createElement('ss-icon');
  icon.setAttribute('icon', 'nonexistent');
  document.body.appendChild(icon);
  
  expect(icon.shadowRoot.innerHTML).toBe('');
});
```

### Snapshot Testing

```javascript
it('should match SVG snapshot', () => {
  const icon = document.createElement('ss-icon');
  icon.setAttribute('icon', 'check');
  document.body.appendChild(icon);
  
  expect(icon.shadowRoot.innerHTML).toMatchSnapshot();
});
```

## Coverage Goals

- **Statements:** > 80%
- **Branches:** > 70%
- **Functions:** > 80%
- **Lines:** > 80%

Exclude `src/svg/` from coverage as it's generated code.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bookklik-technologies) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

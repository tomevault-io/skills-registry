---
name: project-helper
description: Custom skill for project-specific tasks and coding standards Use when this capability is needed.
metadata:
  author: 1shot-labs
---

# Project Helper Skill

This skill provides project-specific assistance, including coding standards enforcement, component generation, and test creation.

## When to Use This Skill

Activate this skill when the user:
- Asks about project conventions or standards
- Wants to create new components or modules
- Needs to generate tests for existing code
- Requests code style checking or formatting
- Asks for project-specific help or guidance

## Project Standards

### File Naming Conventions

- **Components**: PascalCase (`UserProfile.tsx`)
- **Utilities**: camelCase (`formatDate.ts`)
- **Constants**: SCREAMING_SNAKE_CASE in files (`API_ENDPOINTS.ts`)
- **Tests**: Same name with `.test` suffix (`UserProfile.test.tsx`)

### Code Style

Follow the configured code style preset. Key rules:

1. **Imports**: Group and sort imports
   - External packages first
   - Internal modules second
   - Relative imports last
   - Separate groups with blank lines

2. **Exports**: Prefer named exports over default exports

3. **Functions**: Prefer arrow functions for components

4. **Types**: Define types/interfaces above their usage

### Component Structure

React components should follow this structure:

```typescript
// 1. Imports
import React from 'react';
import { useQuery } from '@tanstack/react-query';

import { Button } from '@/components/ui';
import { formatDate } from '@/utils';

import styles from './ComponentName.module.css';

// 2. Types
interface ComponentNameProps {
  title: string;
  onAction: () => void;
}

// 3. Component
export const ComponentName: React.FC<ComponentNameProps> = ({
  title,
  onAction,
}) => {
  // 3a. Hooks
  const { data, isLoading } = useQuery(['key'], fetchData);

  // 3b. Handlers
  const handleClick = () => {
    onAction();
  };

  // 3c. Render helpers
  const renderContent = () => {
    if (isLoading) return <Loading />;
    return <Content data={data} />;
  };

  // 3d. Return
  return (
    <div className={styles.container}>
      <h1>{title}</h1>
      {renderContent()}
      <Button onClick={handleClick}>Action</Button>
    </div>
  );
};
```

## Creating Components

When asked to create a component:

1. **Determine the component type**:
   - Page component (routes)
   - Feature component (self-contained features)
   - UI component (reusable UI elements)
   - Layout component (structural layouts)

2. **Create the file structure**:
   ```
   ComponentName/
   |-- index.ts           # Re-exports
   |-- ComponentName.tsx  # Main component
   |-- ComponentName.test.tsx  # Tests
   +-- ComponentName.module.css  # Styles (if needed)
   ```

3. **Generate boilerplate** following project standards

4. **Add basic tests**

### Component Generation Template

```typescript
// ComponentName.tsx
import React from 'react';

interface {ComponentName}Props {
  // Add props here
}

export const {ComponentName}: React.FC<{ComponentName}Props> = (props) => {
  return (
    <div>
      {/* Component content */}
    </div>
  );
};

// index.ts
export { {ComponentName} } from './{ComponentName}';
export type { {ComponentName}Props } from './{ComponentName}';
```

## Generating Tests

When asked to generate tests:

1. **Identify what to test**:
   - Components: Render, interactions, states
   - Functions: Input/output, edge cases, errors
   - Hooks: State changes, side effects

2. **Follow testing patterns**:

### Component Test Template

```typescript
import { render, screen, fireEvent } from '@testing-library/react';
import { describe, it, expect, vi } from 'vitest';

import { ComponentName } from './ComponentName';

describe('ComponentName', () => {
  it('renders correctly', () => {
    render(<ComponentName title="Test" />);
    expect(screen.getByText('Test')).toBeInTheDocument();
  });

  it('handles user interaction', () => {
    const onAction = vi.fn();
    render(<ComponentName title="Test" onAction={onAction} />);

    fireEvent.click(screen.getByRole('button'));
    expect(onAction).toHaveBeenCalled();
  });

  it('displays loading state', () => {
    render(<ComponentName title="Test" isLoading />);
    expect(screen.getByTestId('loading')).toBeInTheDocument();
  });
});
```

### Function Test Template

```typescript
import { describe, it, expect } from 'vitest';

import { functionName } from './functionName';

describe('functionName', () => {
  it('handles normal input', () => {
    expect(functionName('input')).toBe('expected');
  });

  it('handles edge cases', () => {
    expect(functionName('')).toBe('');
    expect(functionName(null)).toBeNull();
  });

  it('throws on invalid input', () => {
    expect(() => functionName(undefined)).toThrow();
  });
});
```

## Code Style Checking

When asked to check code style:

1. **Review the code** against project standards
2. **Identify issues** with specific line references
3. **Provide fixes** or suggestions
4. **Optionally auto-fix** if enabled in configuration

### Common Issues to Check

- Import organization
- Naming conventions
- TypeScript types (no `any`)
- Proper error handling
- Accessibility attributes
- Performance concerns (unnecessary re-renders)

## Responding to Requests

### "Create a component for [X]"

1. Ask clarifying questions if needed (type, features)
2. Generate the component following standards
3. Include index file and basic tests
4. Explain any decisions made

### "Generate tests for [file]"

1. Read the file to understand its purpose
2. Identify testable aspects
3. Generate comprehensive tests
4. Include edge cases and error scenarios

### "Check this code"

1. Review against project standards
2. List issues with severity (error, warning, suggestion)
3. Provide corrected code snippets
4. Explain the reasoning

### "Help with [project task]"

1. Understand the context
2. Reference relevant project standards
3. Provide step-by-step guidance
4. Offer to generate any needed code

## Configuration

This skill uses the following configuration options:

- `projectName`: Used in generated comments and documentation
- `codeStyle`: Determines which style rules to enforce
- `testFramework`: Determines test syntax and patterns
- `componentPath`: Default location for new components
- `enableAutoFix`: When true, offer to automatically fix issues

## Notes

- Always follow existing patterns in the codebase when available
- Prefer consistency with existing code over strict rules
- Ask for clarification when requirements are ambiguous
- Explain the "why" behind suggestions, not just the "what"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/1shot-labs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

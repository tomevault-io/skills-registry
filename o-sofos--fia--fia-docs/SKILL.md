---
name: fia-docs
description: Generate documentation for Fia components. Use when documenting APIs, creating component docs, or writing usage guides. Use when this capability is needed.
metadata:
  author: o-sofos
---

# Fia Documentation Generator

Generates comprehensive, well-structured documentation for Fia components and modules.

## Usage

```bash
/fia-docs [file-path]
```

## Arguments

- `$0` (optional): File path to document
  - If provided, documents that specific file
  - If omitted, documents the current file or prompts for selection

## What This Skill Generates

### 1. Component Overview

**Includes:**
- Purpose and description
- Use cases and when to use it
- Visual examples for UI components
- Key features and capabilities

**Example:**
```markdown
# Counter Component

A simple counter component with increment, decrement, and reset controls.

## Use Cases
- Numeric input controls
- Quantity selectors
- Step-based navigation
- Progress indicators

## Features
- Reactive count display
- Configurable initial value
- Keyboard shortcuts support
- Customizable styling
```

### 2. Props Documentation

**Includes:**
- TypeScript type definitions
- Required vs optional props
- Default values
- Prop descriptions and examples
- Validation rules

**Example:**
```typescript
/**
 * Counter component properties
 */
export interface CounterProps {
  /**
   * Initial count value
   * @default 0
   */
  initial?: number;

  /**
   * Minimum allowed value
   * @default -Infinity
   */
  min?: number;

  /**
   * Maximum allowed value
   * @default Infinity
   */
  max?: number;

  /**
   * Step increment/decrement amount
   * @default 1
   */
  step?: number;

  /**
   * Callback when count changes
   */
  onChange?: (value: number) => void;
}
```

### 3. Signals Documentation

**Includes:**
- Internal reactive state
- Computed values
- Effect side-effects
- State update patterns

**Example:**
```typescript
/**
 * Internal state
 */
const count = $(props.initial ?? 0); // Current count value

/**
 * Computed values
 */
const isAtMin = $(() => count.value <= (props.min ?? -Infinity));
const isAtMax = $(() => count.value >= (props.max ?? Infinity));

/**
 * Effects
 */
$e(() => {
  // Call onChange callback when count changes
  props.onChange?.(count.value);
});
```

### 4. Events Documentation

**Includes:**
- Event handlers and their signatures
- Custom events emitted
- Event delegation patterns
- Keyboard shortcuts

**Example:**
```markdown
## Events

### Built-in Handlers
- `onclick` on increment button - Increases count by step
- `onclick` on decrement button - Decreases count by step
- `onclick` on reset button - Resets to initial value

### Keyboard Shortcuts
- `ArrowUp` - Increment
- `ArrowDown` - Decrement
- `Escape` - Reset

### Custom Events
None (uses callback props instead)
```

### 5. Usage Examples

**Includes:**
- Basic usage
- Advanced patterns
- Common scenarios
- Integration examples

**Example:**
```typescript
/**
 * Basic usage
 */
Counter({ initial: 0 });

/**
 * With constraints
 */
Counter({
  initial: 5,
  min: 0,
  max: 10,
  step: 1
});

/**
 * With change handler
 */
Counter({
  initial: 0,
  onChange: (value) => {
    console.log("Count changed to:", value);
  }
});

/**
 * In a form
 */
form(() => {
  label("Quantity");
  Counter({
    initial: 1,
    min: 1,
    max: 99,
    onChange: (qty) => updateTotal(qty)
  });
});
```

### 6. Type Exports

**Includes:**
- Public type definitions
- Type parameters and generics
- Type guards and utilities
- Import examples

**Example:**
```typescript
/**
 * Public exports
 */
export { Counter };
export type { CounterProps };

/**
 * Import example
 * @example
 * ```typescript
 * import { Counter, type CounterProps } from "./Counter";
 * ```
 */
```

### 7. Testing Examples

**Includes:**
- Basic test structure
- Testing reactive behavior
- Testing edge cases
- Testing accessibility

**Example:**
```typescript
/**
 * Testing the Counter component
 */
describe("Counter", () => {
  it("starts with initial value", () => {
    const counter = Counter({ initial: 5 });
    expect(counter.textContent).toContain("5");
  });

  it("increments on button click", () => {
    const counter = Counter({ initial: 0 });
    const incrementBtn = counter.querySelector("button:nth-child(2)");
    incrementBtn?.click();
    expect(counter.textContent).toContain("1");
  });

  it("respects min/max constraints", () => {
    const counter = Counter({ initial: 0, min: 0, max: 5 });
    // Test implementation...
  });
});
```

## Documentation Formats

### JSDoc Comments
```typescript
/**
 * Counter component with increment/decrement controls
 *
 * @example
 * ```typescript
 * Counter({ initial: 0 });
 * ```
 *
 * @param props - Component properties
 * @param props.initial - Initial count value (default: 0)
 * @param props.min - Minimum allowed value
 * @param props.max - Maximum allowed value
 * @param props.step - Increment/decrement step (default: 1)
 * @param props.onChange - Callback when count changes
 * @returns HTML element containing the counter UI
 */
export const Counter = (props: CounterProps) => {
  // ...
};
```

### Markdown Documentation
```markdown
# Counter

Counter component with increment/decrement controls.

## Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `initial` | `number` | `0` | Initial count value |
| `min` | `number` | `-Infinity` | Minimum allowed value |
| `max` | `number` | `Infinity` | Maximum allowed value |
| `step` | `number` | `1` | Increment/decrement step |
| `onChange` | `(value: number) => void` | - | Callback when count changes |

## Usage

\`\`\`typescript
import { Counter } from "./Counter";

Counter({ initial: 0 });
\`\`\`
```

## How It Works

1. **Analyze Code** - Parse TypeScript and extract structure
2. **Extract Information** - Find props, signals, events, exports
3. **Generate Docs** - Create JSDoc comments and/or markdown
4. **Add Examples** - Include usage examples based on props
5. **Format Output** - Apply consistent formatting
6. **Update File** - Write documentation back to file

## Documentation Styles

### Inline JSDoc (Default)
- Adds JSDoc comments to the source file
- Integrated with IDE tooltips
- TypeScript-aware
- Best for library code

### Separate Markdown
- Creates standalone `.md` file
- Better for detailed guides
- Includes visual examples
- Best for public documentation

### Both
- JSDoc for API reference
- Markdown for tutorials and guides
- Complete documentation coverage

## Output Example

```typescript
/**
 * Counter component with increment/decrement controls.
 *
 * Provides a simple numeric counter with buttons for incrementing,
 * decrementing, and resetting the value. Supports min/max constraints
 * and custom step values.
 *
 * @example
 * Basic usage
 * ```typescript
 * Counter({ initial: 0 });
 * ```
 *
 * @example
 * With constraints
 * ```typescript
 * Counter({ initial: 5, min: 0, max: 10, step: 1 });
 * ```
 *
 * @param props - Component properties
 * @param props.initial - Initial count value (default: 0)
 * @param props.min - Minimum allowed value (default: -Infinity)
 * @param props.max - Maximum allowed value (default: Infinity)
 * @param props.step - Increment/decrement step (default: 1)
 * @param props.onChange - Callback fired when count changes
 *
 * @returns {SmartElement<"div">} The counter component element
 *
 * @see {@link CounterProps} for complete props interface
 */
export const Counter = (props: CounterProps = {}) => {
  // Implementation...
};
```

## Template

See [templates/doc-template.md](templates/doc-template.md) for the complete documentation template structure.

## Examples

### Document current file
```bash
/fia-docs
```

### Document specific component
```bash
/fia-docs src/components/Counter.ts
```

### Generate markdown docs
```bash
/fia-docs src/components/Counter.ts --format=markdown
```

## Integration

- Use after creating components with `/fia-component`
- Combine with `/fia-fix` to ensure correct patterns
- Run before publishing to ensure complete documentation
- Integrate into CI/CD for documentation validation

## Reference

See [templates/doc-template.md](templates/doc-template.md) for the complete documentation template with all sections and examples.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/o-sofos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

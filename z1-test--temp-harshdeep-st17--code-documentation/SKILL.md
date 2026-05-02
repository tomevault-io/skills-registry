---
name: code-documentation
description: add and manage documentation in code, including constants, variables, functions, classes, and modules Use when this capability is needed.
metadata:
  author: z1-test
---

## What is it?

This skill manages **adding documentation to code**. It covers documenting constants, variables, functions, classes, and modules in a consistent, clear, and maintainable way.

## Success Criteria

- Constants, variables, functions, classes, and modules are documented with clear descriptions.
- Parameters, return values, and types are explained where applicable.
- Documentation uses a consistent style (e.g., JSDoc for JavaScript, docstrings for Python).
- Examples are included when necessary.
- Documentation is updated whenever code changes.

## When to use this skill

- "Document the new function I just wrote."
- "Add docstrings to all classes in this file."
- "Explain the constants and variables in this module."
- "Generate documentation for parameters and return values."
- "Check that all public functions are documented."

## What this skill can do

- **Constants**: Add descriptions, types, and usage notes.
- **Variables**: Explain purpose, type, and constraints.
- **Functions**: Document purpose, parameters, return values, side effects, and examples.
- **Classes**: Describe the class, properties, methods, and usage.
- **Modules**: Provide high-level overview of what the module contains.
- **Style enforcement**: Ensure documentation uses a consistent format.

## What this skill will NOT do

- Execute code.
- Automatically refactor undocumented code.
- Create diagrams or visual documentation.

## How to use this skill

1. **Identify Code Element**: Determine if it’s a constant, variable, function, class, or module.
2. **Select Style**: Choose the documentation format (e.g., JSDoc, Python docstring, TypeScript type annotations).
3. **Document**: Add description, type info, parameters, return values, examples, and notes.
4. **Validate**: Ensure the documentation is clear, concise, and consistent.

## Documentation rules

- **Constants**: Include a short description and purpose. Mention constraints or limits if applicable.
- **Variables**: Describe its role, type, initial value, and constraints.
- **Functions**: Always document:

  - **Description**: What it does
  - **Parameters**: Name, type, and purpose
  - **Return Value**: Type and meaning
  - **Side Effects**: If any
  - **Example**: Optional usage example

- **Classes**: Document:

  - **Class description**: What it represents
  - **Properties**: Name, type, description
  - **Methods**: Include function documentation for each method

- **Modules**: Give a high-level overview of contents and purpose.
- **Consistency**: Use the same format across the project.
- **Update**: When code changes, produce a doc patch or flag outdated docs in the same PR; run linter to verify updates
- **Examples**: Include examples whenever it helps clarify usage.

## Examples

- **Constant**

```javascript
/**
 * Maximum number of users allowed in the system.
 */
const MAX_USERS = 100;
```

- **Variable**

```javascript
/**
 * Current number of active users.
 * Type: number
 */
let userCount = 0;
```

- **Function**

```javascript
/**
 * Calculates the sum of two numbers.
 * @param {number} a - The first number
 * @param {number} b - The second number
 * @returns {number} The sum of a and b
 */
function calculateSum(a, b) {
  return a + b;
}
```

- **Class**

```javascript
/**
 * Represents a user in the system.
 */
class User {
  /**
   * @param {string} name - Name of the user
   * @param {number} age - Age of the user
   */
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  /**
   * Greets the user.
   * @returns {string} Greeting message
   */
  greet() {
    return `Hello, ${this.name}`;
  }
}
```

## Limitations

- Cannot enforce style automatically; depends on developer to apply consistently.
- Cannot document private or internal logic unless explicitly instructed.
- Examples are illustrative; may need adaptation for production code.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/z1-test) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

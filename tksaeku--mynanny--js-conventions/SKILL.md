---
name: js-conventions
description: JavaScript and TypeScript coding conventions. Use when writing any JS/TS code, creating functions, defining variables, handling async operations, structuring exports, naming files, scaffolding components, or choosing styling approaches. Enforces const-by-default, functional patterns, explicit types, DRY principles, no-abbreviation naming, PascalCase component files, camelCase utilities, folder-per-component structure, co-located tests and styles, styled-components with SCSS (never Tailwind or CSS Modules), shared types in types.ts, PascalCase enums, no barrel imports, and displayName on arrow-function components. Use when this capability is needed.
metadata:
  author: tksaeku
---

# JS/TS Conventions

## Naming

### No Abbreviations
- Always use full, descriptive names — never abbreviate variable or function names
- **Allowed exceptions**: `e` (event callbacks), `i`/`j` (loop indices), `req`/`res`/`err` (Express handlers), `props`/`ref` (React)
- Acronyms stay uppercase in camelCase: `apiUrl`, `htmlContent`, `parseXmlDocument`

```typescript
// BAD
const usr = getUser();
const evt = new CustomEvent('click');
const btn = document.querySelector('button');
function calcTax(amt: number): number { ... }
function genId(): string { ... }

// GOOD
const user = getUser();
const event = new CustomEvent('click');
const button = document.querySelector('button');
function calculateTax(amount: number): number { ... }
function generateId(): string { ... }
```

### Casing Rules
- **Variables, public fields, public functions**: `camelCase`
- **Constants and public static class fields**: `ALL_CAPS`
- **Private fields and methods**: `#` prefix (ES private) with `camelCase`

### File Naming
- **Components and classes**: PascalCase — `UserProfile.tsx`, `ApiClient.ts`
  - No extra suffixes like `.component` or `.class` — just the name
  - Each class must live in its own dedicated file; never place classes in utility/helper files
- **All other files**: camelCase — `authHelpers.ts`, `constants.ts`, `useAuth.ts`
- **Extensions**: `.tsx`/`.ts` for TypeScript projects; `.jsx`/`.js` for JavaScript projects
- **Directories**: PascalCase for component directories; camelCase for everything else

```typescript
// GOOD file names
UserProfile.tsx        // component
ApiClient.ts           // class
useAuth.ts             // hook (camelCase — not a component)
authHelpers.ts         // utility
constants.ts           // constants
formatCurrency.ts      // utility function

// BAD file names
UserProfile.component.tsx   // no .component suffix
user-profile.tsx            // not kebab-case
apiClient.ts                // class should be PascalCase
```

```typescript
class DancePartner {
  /** Maximum allowed requests per user per event. */
  static readonly MAX_REQUESTS_PER_EVENT = 5;

  /** Display name shown in partner discovery. */
  displayName: string;

  /** Internal request count not exposed to consumers. */
  #pendingRequestCount = 0;

  /** Validates whether the partner is eligible for the division. */
  validateEligibility(division: string): boolean { ... }

  /** Recalculates internal match score. */
  #recalculateMatchScore(): number { ... }
}
```

## Variables
- `const` by default; `let` only when reassigning
- Never `var`

## Functions
- Regular `function` for named/exported functions
- Arrow functions for callbacks

```typescript
function calculatePay(hours: number, rate: number): number {
  return hours * rate;
}

items.map((item) => item.value);
```

## Control Flow
- **Always use blocks** (`{}`) for `if`, `else`, `for`, `while`, and other control flow — no single-line bodies
- Ternary expressions are fine and preferred for simple conditional assignments

```typescript
// BAD
if (!user) return;
if (index >= items.length) break;
if (!isValid) throw new Error('Invalid');
for (const item of items) processItem(item);

// GOOD
if (!user) {
  return;
}
if (index >= items.length) {
  break;
}
if (!isValid) {
  throw new Error('Invalid');
}
for (const item of items) {
  processItem(item);
}

// Ternaries are fine
const label = isActive ? 'Active' : 'Inactive';
```

## Formatting
- Single quotes, semicolons
- Template literals for interpolation

## Async
- `async/await` over `.then()` chains

## Patterns
- `map`/`filter`/`reduce` over imperative loops
- Immutable data; create new objects/arrays

## TypeScript
- Explicit return types on all functions

## Exports
- Inline exports at declaration
- Exception: React components use `export default ComponentName` at end

```typescript
export function calculateTotal(): number { ... }
export const TAX_RATE = 0.08;

// Component files
function MyComponent() { ... }
export default MyComponent;
```

## Component Scaffolding

Always use a **folder-per-component** structure. Never place components flat in a shared directory.

```
components/
  UserProfile/
    UserProfile.tsx
    UserProfile.test.tsx
    UserProfile.styles.ts
  PaymentForm/
    PaymentForm.tsx
    PaymentForm.test.tsx
    PaymentForm.styles.ts
styles/
  shared.ts                  # shared styled-components live outside component folders
```

- Style and test files are **always co-located** next to their component
- Shared styles belong in a separate shared directory (e.g., `styles/`, `shared/`)
- Component directory names are **PascalCase**, matching the component name
- Page-level components follow the same folder-per-component pattern (e.g., `pages/Dashboard/Dashboard.tsx`)

## displayName

Always add `displayName` to **arrow-function components**. Not needed for `function` declarations — React infers the name automatically.

```typescript
// Arrow function — displayName REQUIRED
const UserProfile = () => {
  return <div>...</div>;
};
UserProfile.displayName = 'UserProfile';
export default UserProfile;

// Function declaration — displayName NOT needed
function UserProfile() {
  return <div>...</div>;
}
export default UserProfile;
```

## Barrel Exports

**Never use barrel files (`index.ts` re-exports).** Always import directly from the source file.

Barrels hurt tree-shaking, introduce circular dependency risks, and obscure where code lives.

```typescript
// BAD — importing through a barrel
import { UserProfile } from '@components';
import { TransactionCore } from '@core/transactions';

// GOOD — importing directly from source
import UserProfile from './components/UserProfile/UserProfile';
import { TransactionCore } from '@core/transactions/TransactionCore';
```

## Component File Organization
- Component files should **only** export the component itself
- Extract all exported constants to a shared `constants.ts` file
- Extract all exported helper/util functions to a `utils.ts` or dedicated util file
- Private helpers used only within the component may remain in the file (unexported)

```typescript
// BAD - MyComponent.tsx exports non-component items
export const MAX_ITEMS = 10;
export function formatLabel(s: string): string { ... }
function MyComponent() { ... }
export default MyComponent;

// GOOD - separated concerns
// constants.ts
export const MAX_ITEMS = 10;

// utils.ts
export function formatLabel(s: string): string { ... }

// MyComponent.tsx
import { MAX_ITEMS } from './constants';
import { formatLabel } from './utils';
function MyComponent() { ... }
export default MyComponent;
```

## Constants
- String literals and magic numbers go in a shared `constants.ts`
- ALL_CAPS naming

```typescript
export const DEFAULT_HOURLY_RATE = 25;
export const MILEAGE_RATE = 0.67;
```

## DRY
- Code repeated >2 times: extract to reusable function
- All meaningful logic requires unit tests

## JSDoc Comments
- Add JSDoc to every constant, function, and method
- Describe **what** it does (context/intention) and **how** it works (internal logic)
- TypeScript files: no type annotations in JSDoc (TS handles types)
- JavaScript files: include full type annotations

### TypeScript Example

```typescript
/**
 * Calculates gross pay for an employee based on hours worked.
 * Multiplies hours by rate directly without overtime consideration.
 * Used by payroll module for standard hourly employees.
 */
export function calculatePay(hours: number, rate: number): number {
  return hours * rate;
}

/** Default rate applied when employee has no custom rate assigned. */
export const DEFAULT_HOURLY_RATE = 25;
```

### JavaScript Example

```javascript
/**
 * Calculates gross pay for an employee based on hours worked.
 * Multiplies hours by rate directly without overtime consideration.
 * Used by payroll module for standard hourly employees.
 * @param {number} hours - Total hours worked in pay period
 * @param {number} rate - Hourly rate in dollars
 * @returns {number} Gross pay amount
 */
export function calculatePay(hours, rate) {
  return hours * rate;
}

/** @type {number} Default rate applied when employee has no custom rate assigned. */
export const DEFAULT_HOURLY_RATE = 25;
```

## Styling

- Use **styled-components** with **SCSS** syntax for all component styling
- **Never use Tailwind CSS**
- **Never use CSS Modules** (`.module.css`)
- Co-locate styles in a `ComponentName.styles.ts` file next to the component
- Shared styles go in a separate `styles/` directory

```typescript
// UserProfile.styles.ts
import styled from 'styled-components';

export const ProfileContainer = styled.div`
  display: flex;
  flex-direction: column;
  padding: 16px;

  &:hover {
    background-color: #f5f5f5;
  }

  .avatar {
    border-radius: 50%;
    width: 48px;
    height: 48px;
  }
`;

// UserProfile.tsx
import { ProfileContainer } from './UserProfile.styles';

function UserProfile() {
  return (
    <ProfileContainer>
      <img className="avatar" src={avatarUrl} alt="Profile" />
    </ProfileContainer>
  );
}
export default UserProfile;
```

## Types

- Shared types and interfaces live in a dedicated `types.ts` file
- Only keep a type/interface in the same file if **no other file uses it**
- Name type files `types.ts` (camelCase, like all non-component files)

```typescript
// types.ts — shared across multiple files
export interface UserProfile {
  id: string;
  displayName: string;
  email: string;
}

export type PaymentStatus = 'pending' | 'completed' | 'failed';

// UserCard.tsx — private type used only here, fine to keep inline
interface UserCardProps {
  user: UserProfile;
  onSelect: (id: string) => void;
}
```

## Enums

- Enum names use **PascalCase**
- Enum members use **PascalCase**

```typescript
enum PaymentStatus {
  Pending,
  Completed,
  Failed,
  Refunded,
}

enum UserRole {
  Admin,
  Editor,
  Viewer,
}
```

## Date Handling
- **Never** use `new Date("YYYY-MM-DD")` - it parses as UTC midnight, causing off-by-one day bugs in local time
- Parse date strings manually as local time:

```typescript
// WRONG - parses as UTC, shows previous day in US timezones
const date = new Date("2026-01-26"); // Could show Jan 25!

// CORRECT - parse as local time
function parseLocalDate(dateString: string): Date {
  const [year, month, day] = dateString.split('-').map(Number);
  return new Date(year, month - 1, day);
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tksaeku) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

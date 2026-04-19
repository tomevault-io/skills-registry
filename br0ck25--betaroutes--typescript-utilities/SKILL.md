---
name: typescript-utilities
description: Expert patterns for writing reusable, type-safe TypeScript utilities and helpers. Use when creating utility functions, shared helpers, type definitions, or asking about "TypeScript utils", "helper functions", "type safety", "generics", or "TypeScript patterns". Use when this capability is needed.
metadata:
  author: br0ck25
---

# TypeScript Utilities Skill

## Purpose
Provides expert guidance for creating clean, type-safe, reusable utility functions and helpers.

## Utility Organization

### Folder Structure
```
src/lib/utils/
├── format/
│   ├── currency.ts
│   ├── dates.ts
│   └── numbers.ts
├── validation/
│   ├── email.ts
│   ├── phone.ts
│   └── forms.ts
├── array/
│   ├── groupBy.ts
│   ├── unique.ts
│   └── sort.ts
├── string/
│   ├── slugify.ts
│   ├── truncate.ts
│   └── capitalize.ts
└── table/
    ├── filters.ts
    ├── pagination.ts
    └── selection.ts
```

### File Naming
- Use **descriptive names**: `formatCurrency.ts` not `utils.ts`
- Group by **domain**: format/, validation/, not helpers/
- One **primary export** per file (can have related helpers)

## Type-Safe Utility Patterns

### Generic Filters
```typescript
// src/lib/utils/table/filters.ts

export interface FilterConfig<T> {
  searchQuery?: string;
  dateRange?: { start: string; end: string };
  category?: string;
  customFilters?: Partial<T>;
}

export function filterRecords<T extends Record<string, any>>(
  records: T[],
  config: FilterConfig<T>,
  customMatcher?: (item: T, query: string) => boolean
): T[] {
  return records.filter(item => {
    // Date range (if item has date property)
    if (config.dateRange && 'date' in item) {
      const date = item.date as string;
      if (config.dateRange.start && date < config.dateRange.start) return false;
      if (config.dateRange.end && date > config.dateRange.end) return false;
    }
    
    // Category (if item has category property)
    if (config.category && config.category !== 'all' && 'category' in item) {
      if (item.category !== config.category) return false;
    }
    
    // Search query
    if (config.searchQuery && customMatcher) {
      if (!customMatcher(item, config.searchQuery)) return false;
    }
    
    // Custom filters
    if (config.customFilters) {
      for (const [key, value] of Object.entries(config.customFilters)) {
        if (item[key] !== value) return false;
      }
    }
    
    return true;
  });
}

// Usage:
const filtered = filterRecords(expenses, {
  dateRange: { start: '2024-01-01', end: '2024-12-31' },
  category: 'fuel',
  customFilters: { taxDeductible: true }
}, (expense, query) => 
  expense.description.toLowerCase().includes(query.toLowerCase())
);
```

### Generic Sorting
```typescript
export type SortDirection = 'asc' | 'desc';

export function sortRecords<T>(
  records: T[],
  key: keyof T,
  direction: SortDirection = 'asc'
): T[] {
  const sorted = [...records].sort((a, b) => {
    const aVal = a[key];
    const bVal = b[key];
    
    // Handle null/undefined
    if (aVal == null && bVal == null) return 0;
    if (aVal == null) return direction === 'asc' ? -1 : 1;
    if (bVal == null) return direction === 'asc' ? 1 : -1;
    
    // Handle strings
    if (typeof aVal === 'string' && typeof bVal === 'string') {
      return direction === 'asc' 
        ? aVal.localeCompare(bVal)
        : bVal.localeCompare(aVal);
    }
    
    // Handle numbers/dates
    if (aVal < bVal) return direction === 'asc' ? -1 : 1;
    if (aVal > bVal) return direction === 'asc' ? 1 : -1;
    return 0;
  });
  
  return sorted;
}

// Advanced: Multiple sort keys
export function sortByMultiple<T>(
  records: T[],
  sortKeys: Array<{ key: keyof T; direction: SortDirection }>
): T[] {
  return [...records].sort((a, b) => {
    for (const { key, direction } of sortKeys) {
      const aVal = a[key];
      const bVal = b[key];
      
      if (aVal === bVal) continue;
      
      const comparison = aVal < bVal ? -1 : 1;
      return direction === 'asc' ? comparison : -comparison;
    }
    return 0;
  });
}
```

### Generic Pagination
```typescript
export interface PaginationState {
  currentPage: number;
  itemsPerPage: number;
}

export interface PaginationResult<T> {
  items: T[];
  totalPages: number;
  totalItems: number;
  startIndex: number;
  endIndex: number;
  hasNext: boolean;
  hasPrevious: boolean;
}

export function paginateRecords<T>(
  records: T[],
  state: PaginationState
): PaginationResult<T> {
  const totalItems = records.length;
  const totalPages = Math.ceil(totalItems / state.itemsPerPage);
  const startIndex = (state.currentPage - 1) * state.itemsPerPage;
  const endIndex = Math.min(startIndex + state.itemsPerPage, totalItems);
  
  return {
    items: records.slice(startIndex, endIndex),
    totalPages,
    totalItems,
    startIndex,
    endIndex,
    hasNext: state.currentPage < totalPages,
    hasPrevious: state.currentPage > 1
  };
}
```

## Format Utilities

### Currency
```typescript
// src/lib/utils/format/currency.ts

export function formatCurrency(
  cents: number,
  options: { 
    locale?: string;
    currency?: string;
    showCents?: boolean;
  } = {}
): string {
  const {
    locale = 'en-US',
    currency = 'USD',
    showCents = true
  } = options;
  
  const amount = cents / 100;
  
  return new Intl.NumberFormat(locale, {
    style: 'currency',
    currency,
    minimumFractionDigits: showCents ? 2 : 0,
    maximumFractionDigits: showCents ? 2 : 0
  }).format(amount);
}

export function parseCurrency(value: string): number {
  // Remove currency symbols, commas, spaces
  const cleaned = value.replace(/[$,\s]/g, '');
  const parsed = parseFloat(cleaned);
  
  if (isNaN(parsed)) return 0;
  
  // Convert to cents
  return Math.round(parsed * 100);
}

// Test cases in comments
/*
formatCurrency(123456) // "$1,234.56"
formatCurrency(123456, { showCents: false }) // "$1,235"
parseCurrency("$1,234.56") // 123456
parseCurrency("1234.56") // 123456
*/
```

### Dates
```typescript
// src/lib/utils/format/dates.ts

export type DateFormat = 'short' | 'medium' | 'long' | 'full';

export function formatDate(
  date: Date | string,
  format: DateFormat = 'medium',
  locale: string = 'en-US'
): string {
  const d = typeof date === 'string' ? new Date(date) : date;
  
  const formatOptions: Record<DateFormat, Intl.DateTimeFormatOptions> = {
    short: { month: 'numeric', day: 'numeric', year: '2-digit' },
    medium: { month: 'short', day: 'numeric', year: 'numeric' },
    long: { month: 'long', day: 'numeric', year: 'numeric' },
    full: { weekday: 'long', month: 'long', day: 'numeric', year: 'numeric' }
  };
  
  return new Intl.DateTimeFormat(locale, formatOptions[format]).format(d);
}

export function getRelativeTime(
  date: Date | string,
  locale: string = 'en-US'
): string {
  const d = typeof date === 'string' ? new Date(date) : date;
  const now = new Date();
  const diffMs = now.getTime() - d.getTime();
  const diffSecs = Math.floor(diffMs / 1000);
  const diffMins = Math.floor(diffSecs / 60);
  const diffHours = Math.floor(diffMins / 60);
  const diffDays = Math.floor(diffHours / 24);
  
  const rtf = new Intl.RelativeTimeFormat(locale, { numeric: 'auto' });
  
  if (diffSecs < 60) return rtf.format(-diffSecs, 'second');
  if (diffMins < 60) return rtf.format(-diffMins, 'minute');
  if (diffHours < 24) return rtf.format(-diffHours, 'hour');
  if (diffDays < 30) return rtf.format(-diffDays, 'day');
  
  return formatDate(d, 'medium', locale);
}
```

## Validation Utilities

### Email
```typescript
// src/lib/utils/validation/email.ts

export function isValidEmail(email: string): boolean {
  const pattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return pattern.test(email);
}

export function normalizeEmail(email: string): string {
  return email.toLowerCase().trim();
}

export function validateEmailWithReason(
  email: string
): { valid: boolean; reason?: string } {
  if (!email) {
    return { valid: false, reason: 'Email is required' };
  }
  
  if (email.length > 254) {
    return { valid: false, reason: 'Email is too long' };
  }
  
  if (!isValidEmail(email)) {
    return { valid: false, reason: 'Email format is invalid' };
  }
  
  return { valid: true };
}
```

### Forms
```typescript
// src/lib/utils/validation/forms.ts

export type ValidationRule<T> = {
  validate: (value: T) => boolean;
  message: string;
};

export type ValidationRules<T> = {
  [K in keyof T]?: ValidationRule<T[K]>[];
};

export type ValidationErrors<T> = {
  [K in keyof T]?: string;
};

export function validateForm<T extends Record<string, any>>(
  data: T,
  rules: ValidationRules<T>
): { valid: boolean; errors: ValidationErrors<T> } {
  const errors: ValidationErrors<T> = {};
  
  for (const field in rules) {
    const fieldRules = rules[field];
    if (!fieldRules) continue;
    
    const value = data[field];
    
    for (const rule of fieldRules) {
      if (!rule.validate(value)) {
        errors[field] = rule.message;
        break; // Stop at first error per field
      }
    }
  }
  
  return {
    valid: Object.keys(errors).length === 0,
    errors
  };
}

// Usage:
interface LoginForm {
  email: string;
  password: string;
}

const rules: ValidationRules<LoginForm> = {
  email: [
    { validate: (v) => !!v, message: 'Email is required' },
    { validate: isValidEmail, message: 'Email is invalid' }
  ],
  password: [
    { validate: (v) => !!v, message: 'Password is required' },
    { validate: (v) => v.length >= 8, message: 'Password must be 8+ characters' }
  ]
};

const { valid, errors } = validateForm({ email: 'test', password: '123' }, rules);
```

## Array Utilities

### GroupBy
```typescript
// src/lib/utils/array/groupBy.ts

export function groupBy<T, K extends string | number>(
  items: T[],
  getKey: (item: T) => K
): Record<K, T[]> {
  return items.reduce((acc, item) => {
    const key = getKey(item);
    if (!acc[key]) {
      acc[key] = [];
    }
    acc[key].push(item);
    return acc;
  }, {} as Record<K, T[]>);
}

// Usage:
const expenses = [
  { category: 'fuel', amount: 100 },
  { category: 'fuel', amount: 150 },
  { category: 'maintenance', amount: 200 }
];

const byCategory = groupBy(expenses, e => e.category);
// { fuel: [...], maintenance: [...] }
```

### Unique
```typescript
export function unique<T>(items: T[]): T[] {
  return [...new Set(items)];
}

export function uniqueBy<T, K>(
  items: T[],
  getKey: (item: T) => K
): T[] {
  const seen = new Set<K>();
  return items.filter(item => {
    const key = getKey(item);
    if (seen.has(key)) return false;
    seen.add(key);
    return true;
  });
}

// Usage:
const users = [
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' },
  { id: 1, name: 'Alice (duplicate)' }
];

const uniqueUsers = uniqueBy(users, u => u.id);
// [{ id: 1, name: 'Alice' }, { id: 2, name: 'Bob' }]
```

### Chunking
```typescript
export function chunk<T>(items: T[], size: number): T[][] {
  const chunks: T[][] = [];
  for (let i = 0; i < items.length; i += size) {
    chunks.push(items.slice(i, i + size));
  }
  return chunks;
}

// Usage:
const numbers = [1, 2, 3, 4, 5, 6, 7, 8];
const chunked = chunk(numbers, 3);
// [[1, 2, 3], [4, 5, 6], [7, 8]]
```

## String Utilities

### Slugify
```typescript
// src/lib/utils/string/slugify.ts

export function slugify(text: string): string {
  return text
    .toLowerCase()
    .trim()
    .replace(/[^\w\s-]/g, '') // Remove special chars
    .replace(/[\s_-]+/g, '-') // Replace spaces/underscores with hyphens
    .replace(/^-+|-+$/g, ''); // Remove leading/trailing hyphens
}

// Usage:
slugify("Hello World! This is a Test") // "hello-world-this-is-a-test"
```

### Truncate
```typescript
export function truncate(
  text: string,
  length: number,
  suffix: string = '...'
): string {
  if (text.length <= length) return text;
  return text.slice(0, length - suffix.length) + suffix;
}

export function truncateWords(
  text: string,
  wordCount: number,
  suffix: string = '...'
): string {
  const words = text.split(/\s+/);
  if (words.length <= wordCount) return text;
  return words.slice(0, wordCount).join(' ') + suffix;
}
```

## Testing Utilities

### Test Helpers
```typescript
// src/lib/utils/test/helpers.ts

export function createMockUser(overrides = {}) {
  return {
    id: 'user-123',
    email: 'test@example.com',
    name: 'Test User',
    ...overrides
  };
}

export function createMockTrip(overrides = {}) {
  return {
    id: 'trip-123',
    date: '2024-01-01',
    totalMiles: 100,
    profit: 5000,
    ...overrides
  };
}

// Usage in tests:
const user = createMockUser({ email: 'custom@test.com' });
```

## Best Practices

### ✅ Do
- **Single responsibility** - each utility does one thing well
- **Pure functions** - no side effects
- **Type safety** - use generics appropriately
- **Document with JSDoc** - explain parameters and return values
- **Include examples** - show usage in comments
- **Handle edge cases** - null, undefined, empty arrays, etc.

### ❌ Don't
- **Kitchen sink files** - 50+ functions in one file
- **Mutation** - modify parameters, return new values
- **Global state** - utilities should be stateless
- **Framework coupling** - utilities should be framework-agnostic
- **Over-engineering** - keep it simple

## Quick Reference

### When to Create a Utility
- Used in 2+ places
- Pure logic (no UI, no side effects)
- Can be tested in isolation
- Makes code more readable

### File Organization
```
utils/
  domain/        # Business logic (trips/, expenses/)
  format/        # Display formatting
  validation/    # Input validation
  array/         # Array manipulation
  string/        # String manipulation
  table/         # Data table utilities
```

### Naming Conventions
- Verbs for actions: `formatCurrency`, `validateEmail`
- Adjectives for checks: `isValid`, `isEmpty`
- Get for retrieval: `getUserId`, `getTotal`
- Create for factories: `createMockUser`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/br0ck25) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

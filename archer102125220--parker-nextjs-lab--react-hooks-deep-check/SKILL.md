---
name: react-hooks-deep-check
description: Comprehensive deep check procedures for React hooks usage Use when this capability is needed.
metadata:
  author: archer102125220
---

# React Hooks Deep Check

## 🎯 When to Use This Skill

Use this skill when:
- **Reviewing React components** for hooks optimization
- **Refactoring React hooks** to use proper patterns
- **Performing code quality audits** on React codebase
- **The task explicitly mentions "React Hooks refactoring"**
- **User asks to "check" or "review" components**

## ⚠️ Critical Rule

**You MUST perform TWO rounds of checks:**
1. **Round 1**: Basic checks (import type, useCallback, useMemo)
2. **Round 2**: Deep checks (8 anti-patterns below)

**If you skip Round 2, you MUST explicitly state:**
> "⚠️ I have only performed basic checks. Deep checks are still required."

---

## 🔍 8 Deep Check Items

### 1. Props → State Synchronization

**Anti-Pattern**: Using `useEffect` to sync props to state

**Why it's wrong**: 
- Causes unnecessary re-renders
- Creates derived state that should be computed
- Adds complexity

**How to detect**:
```typescript
// 🔍 Search Pattern
useEffect(() => {
  setState(props.xxx);
}, [props.xxx]);
```

**Correct Pattern**:
```typescript
// ✅ Option 1: Use useMemo
const value = useMemo(() => props.xxx, [props.xxx]);

// ✅ Option 2: Use props directly
<Component value={props.xxx} />
```

**Example**:
```typescript
// ❌ WRONG
function Link({ nonce }: Props) {
  const [clientNonce, setClientNonce] = useState<string>('');
  
  useEffect(() => {
    if (typeof nonce === 'string' && nonce !== '') {
      setClientNonce(nonce);
    }
  }, [nonce]);
  
  return <MuiLink nonce={clientNonce} />;
}

// ✅ CORRECT
function Link({ nonce }: Props) {
  const clientNonce = useMemo(
    () => (typeof nonce === 'string' && nonce !== '' ? nonce : ''),
    [nonce]
  );
  
  return <MuiLink nonce={clientNonce} />;
}
```

---

### 2. External State Subscription

**Anti-Pattern**: Using `useEffect` + `addEventListener`

**Why it's wrong**:
- Not optimized for external state
- Can cause tearing in concurrent rendering
- React 18+ provides better solution

**How to detect**:
```typescript
// 🔍 Search Pattern
useEffect(() => {
  window.addEventListener('resize', handler);
  return () => window.removeEventListener('resize', handler);
}, []);
```

**Correct Pattern**:
```typescript
// ✅ Use useSyncExternalStore
const windowSize = useSyncExternalStore(
  subscribe,
  getSnapshot,
  getServerSnapshot
);

// ✅ Or use existing hook
const { width, height } = useWindowSize();
```

**Example**:
```typescript
// ❌ WRONG
function ClientProvider() {
  useEffect(() => {
    function handleResize() {
      store.dispatch({ type: 'setWindowSize', payload: { width: window.innerWidth } });
    }
    window.addEventListener('resize', handleResize);
    handleResize();
    return () => window.removeEventListener('resize', handleResize);
  }, []);
}

// ✅ CORRECT
function ClientProvider() {
  const { width, height } = useWindowSize(); // Uses useSyncExternalStore internally
  
  useEffect(() => {
    store.dispatch({ type: 'setWindowSize', payload: { width, height } });
  }, [width, height]);
}
```

---

### 3. Multiple Related States

**Anti-Pattern**: Using 5+ `useState` for related state

**Why it's wrong**:
- Hard to maintain state consistency
- Complex update logic
- Difficult to test

**How to detect**:
```typescript
// 🔍 Count useState calls
// If 5+ and they're related, use useReducer
```

**Correct Pattern**:
```typescript
// ✅ Use useReducer
const [state, dispatch] = useReducer(reducer, initialState);
```

**Example**:
```typescript
// ❌ WRONG
function Message() {
  const [clientNonce, setClientNonce] = useState<string>('');
  const [open, setOpen] = useState<boolean>(false);
  const [messageText, setMessageText] = useState<string>('');
  const [messageType, setMessageType] = useState<'success' | 'error'>('success');
  
  // Complex logic managing all these states...
}

// ✅ CORRECT
type MessageState = {
  clientNonce: string;
  open: boolean;
  text: string;
  type: 'success' | 'error';
};

type MessageAction =
  | { type: 'SHOW'; payload: { text: string; messageType: 'success' | 'error' } }
  | { type: 'HIDE' }
  | { type: 'SET_NONCE'; payload: string };

function messageReducer(state: MessageState, action: MessageAction): MessageState {
  switch (action.type) {
    case 'SHOW':
      return { ...state, open: true, text: action.payload.text, type: action.payload.messageType };
    case 'HIDE':
      return { ...state, open: false };
    case 'SET_NONCE':
      return { ...state, clientNonce: action.payload };
    default:
      return state;
  }
}

function Message() {
  const [state, dispatch] = useReducer(messageReducer, {
    clientNonce: '',
    open: false,
    text: '',
    type: 'success',
  });
}
```

---

### 4. Uncached Calculations

**Anti-Pattern**: Expensive calculations without `useMemo`

**Why it's wrong**:
- Re-calculates on every render
- Wastes CPU cycles
- Can cause performance issues

**How to detect**:
```typescript
// 🔍 Search Pattern
const result = array.filter(...).map(...);
const isActive = pathname.startsWith('/xxx');
const config = { ...defaultConfig, ...props.config };
```

**Correct Pattern**:
```typescript
// ✅ Use useMemo
const result = useMemo(() => array.filter(...).map(...), [array]);
const isActive = useMemo(() => pathname.startsWith('/xxx'), [pathname]);
const config = useMemo(() => ({ ...defaultConfig, ...props.config }), [props.config]);
```

**Example**:
```typescript
// ❌ WRONG
function I18nList({ pathname }: Props) {
  const isZhTw = pathname.startsWith('/zh-tw');  // Recalculates every render
  const isEn = pathname.startsWith('/en');
  
  return <div>{/* ... */}</div>;
}

// ✅ CORRECT
function I18nList({ pathname }: Props) {
  const isZhTw = useMemo(() => pathname.startsWith('/zh-tw'), [pathname]);
  const isEn = useMemo(() => pathname.startsWith('/en'), [pathname]);
  
  return <div>{/* ... */}</div>;
}
```

---

### 5. Effect with Callback Dependencies

**Anti-Pattern**: Using `useRef` + `useCallback` pattern in effects

**Why it's wrong**:
- Verbose and hard to understand
- React 19 provides better solution

**How to detect**:
```typescript
// 🔍 Search Pattern
const callbackRef = useRef(callback);
useEffect(() => {
  callbackRef.current = callback;
}, [callback]);

useEffect(() => {
  callbackRef.current();
}, []);
```

**Correct Pattern**:
```typescript
// ✅ Use useEffectEvent (React 19)
const onEvent = useEffectEvent(() => {
  callback();
});

useEffect(() => {
  onEvent();
}, []);
```

---

### 6. Visual Synchronization

**Anti-Pattern**: Using `useEffect` for visual state sync

**Why it's wrong**:
- Runs after paint, causes flicker
- Not synchronous with DOM updates

**How to detect**:
```typescript
// 🔍 Search Pattern: syncing visual state
useEffect(() => {
  setInternalValue(externalValue);
}, [externalValue]);
```

**Correct Pattern**:
```typescript
// ✅ Use useLayoutEffect
useLayoutEffect(() => {
  setInternalValue(externalValue);
}, [externalValue]);
```

---

### 7. Callback to Memoized Children

**Anti-Pattern**: Inline functions to memoized components

**Why it's wrong**:
- Breaks memoization
- Causes unnecessary re-renders

**How to detect**:
```typescript
// 🔍 Search Pattern
const MemoChild = memo(Child);
<MemoChild onClick={() => doSomething()} />
```

**Correct Pattern**:
```typescript
// ✅ Use useCallback
const handleClick = useCallback(() => doSomething(), []);
<MemoChild onClick={handleClick} />
```

---

### 8. Non-Render Values

**Anti-Pattern**: Using `useState` for values that don't trigger re-render

**Why it's wrong**:
- Causes unnecessary re-renders
- useState is for render-triggering state

**How to detect**:
```typescript
// 🔍 Search Pattern: timer IDs, previous values
const [timerId, setTimerId] = useState<number | null>(null);
const [prevValue, setPrevValue] = useState(value);
```

**Correct Pattern**:
```typescript
// ✅ Use useRef
const timerIdRef = useRef<number | null>(null);
const prevValueRef = useRef(value);
```

---

## 📋 Deep Check Workflow

### Step 1: Preparation
```typescript
// 1. Read the component file
// 2. Understand overall structure
// 3. Note any obvious issues
```

### Step 2: Count States
```typescript
// Count useState calls
// If 5+, check if they're related
// If related, flag for useReducer
```

### Step 3: Check Effects
```typescript
// For each useEffect:
// - Is it syncing props to state? → useMemo
// - Is it adding event listeners? → useSyncExternalStore
// - Is it for visual sync? → useLayoutEffect
```

### Step 4: Check Calculations
```typescript
// Look for:
// - .filter(), .map(), .reduce()
// - .startsWith(), .includes()
// - Object spreading
// If expensive, flag for useMemo
```

### Step 5: Check Callbacks
```typescript
// For each callback:
// - Is it passed to memo component? → useCallback
// - Is it inline arrow function? → extract and useCallback
```

### Step 6: Document Findings
```markdown
## Component: [Name]

### Issues Found:
1. [Issue 1] - Priority: 🔴/🟡
2. [Issue 2] - Priority: 🔴/🟡

### Recommendations:
1. [Recommendation 1]
2. [Recommendation 2]
```

---

## 🛠️ Automated Search Methods

### Using grep_search Tool
```typescript
// Search for useEffect syncing props
grep_search({
  SearchPath: "components",
  Query: "useEffect.*setState",
  IsRegex: true,
  MatchPerLine: true
});

// Search for addEventListener
grep_search({
  SearchPath: "components",
  Query: "addEventListener",
  MatchPerLine: true
});
```

### Using find_by_name Tool
```typescript
// Find all component files
find_by_name({
  SearchDirectory: "components",
  Pattern: "*.tsx",
  Extensions: ["tsx"]
});
```

---

## ✅ When to Skip Deep Checks

- Simple presentational components (< 50 lines)
- Components with only 1-2 useState
- Components with no useEffect
- Demo/test components
- Third-party library wrappers

For all other components, **deep checks are mandatory**.

---

## 📝 Check Report Template

```markdown
# React Hooks Deep Check Report

**Component**: [Component Name]
**File**: [File Path]
**Date**: [YYYY-MM-DD]

## Round 1: Basic Check ✅
- [x] Import type syntax correct
- [x] useCallback used appropriately
- [x] useMemo used appropriately

## Round 2: Deep Check

| Item | Status | Details |
|------|--------|---------|
| Props → State | ✅/❌ | ... |
| External Subscription | ✅/❌ | ... |
| Multiple States | ✅/❌ | ... |
| Uncached Calculations | ✅/❌ | ... |
| Callback Dependencies | ✅/❌ | ... |
| Visual Sync | ✅/❌ | ... |
| Memoized Children | ✅/❌ | ... |
| Non-Render Values | ✅/❌ | ... |

## Issues Found

### 🔴 High Priority
1. [Issue description]
   - Current: [code snippet]
   - Recommended: [code snippet]

### 🟡 Medium Priority
1. [Issue description]
   - Current: [code snippet]
   - Recommended: [code snippet]

## Summary

- Total issues: [N]
- High priority: [N]
- Medium priority: [N]
- Estimated effort: [time]
```

---

## 🔗 Related Resources

- `GEMINI.md` - React Hooks Deep Check Policy
- `.agent/skills/react-hooks-selection/SKILL.md` - Hook selection guide
- `.agent/workflows/react-hooks-check.md` - Automated workflow

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/archer102125220) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

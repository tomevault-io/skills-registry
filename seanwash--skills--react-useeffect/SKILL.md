---
name: react-useeffect
description: Guides React development to avoid unnecessary useEffect usage. Provides alternatives like computing during render, useMemo, key props, and event handlers. Use when writing React components, reviewing React code, or when useEffect is being considered for state updates, derived data, or event handling. Use when this capability is needed.
metadata:
  author: seanwash
---

# React useEffect Guidelines

Effects are an escape hatch for synchronizing with **external systems**. If no external system is involved, you likely don't need an Effect.

## Decision Guide

| Situation | Solution |
|-----------|----------|
| Derive value from props/state | Calculate during render |
| Cache expensive calculation | `useMemo` |
| Reset all state when prop changes | Pass `key` to component |
| Reset some state when prop changes | Store ID, derive during render |
| Respond to user interaction | Event handler |
| Sync with external system | `useEffect` |
| Subscribe to external store | `useSyncExternalStore` |

## Anti-Patterns

### Derived State

```jsx
// 🔴 Avoid
const [fullName, setFullName] = useState('');
useEffect(() => {
  setFullName(firstName + ' ' + lastName);
}, [firstName, lastName]);

// ✅ Calculate during render
const fullName = firstName + ' ' + lastName;
```

### Expensive Calculations

```jsx
// 🔴 Avoid
const [filtered, setFiltered] = useState([]);
useEffect(() => {
  setFiltered(getFilteredTodos(todos, filter));
}, [todos, filter]);

// ✅ useMemo
const filtered = useMemo(
  () => getFilteredTodos(todos, filter),
  [todos, filter]
);
```

### Resetting State on Prop Change

```jsx
// 🔴 Avoid
useEffect(() => {
  setComment('');
}, [userId]);

// ✅ Use key to reset component
<Profile userId={userId} key={userId} />
```

### Partial State Reset

```jsx
// 🔴 Avoid
useEffect(() => {
  setSelection(null);
}, [items]);

// ✅ Store ID, derive selection
const [selectedId, setSelectedId] = useState(null);
const selection = items.find(item => item.id === selectedId) ?? null;
```

### User Event Handling

```jsx
// 🔴 Avoid - runs on mount, refresh, etc.
useEffect(() => {
  if (product.isInCart) {
    showNotification(`Added ${product.name} to cart!`);
  }
}, [product]);

// ✅ Handle in event handler
function handleBuyClick() {
  addToCart(product);
  showNotification(`Added ${product.name} to cart!`);
}
```

### Event-Triggered Requests

```jsx
// 🔴 Avoid
useEffect(() => {
  if (jsonToSubmit !== null) {
    post('/api/register', jsonToSubmit);
  }
}, [jsonToSubmit]);

// ✅ In event handler
function handleSubmit(e) {
  e.preventDefault();
  post('/api/register', { firstName, lastName });
}
```

### Effect Chains

```jsx
// 🔴 Avoid - cascading re-renders
useEffect(() => { ... setGoldCardCount(...) }, [card]);
useEffect(() => { ... setRound(...) }, [goldCardCount]);
useEffect(() => { ... setIsGameOver(...) }, [round]);

// ✅ Calculate during render + handle in event
const isGameOver = round > 5;

function handlePlaceCard(nextCard) {
  setCard(nextCard);
  if (nextCard.gold) {
    if (goldCardCount < 3) setGoldCardCount(goldCardCount + 1);
    else { setGoldCardCount(0); setRound(round + 1); }
  }
}
```

### Notifying Parent

```jsx
// 🔴 Avoid
useEffect(() => {
  onChange(isOn);
}, [isOn, onChange]);

// ✅ Call in same event handler
function updateToggle(nextIsOn) {
  setIsOn(nextIsOn);
  onChange(nextIsOn);
}
```

### Passing Data to Parent

```jsx
// 🔴 Avoid
function Child({ onFetched }) {
  const data = useSomeAPI();
  useEffect(() => {
    if (data) onFetched(data);
  }, [data, onFetched]);
}

// ✅ Lift data fetching to parent
function Parent() {
  const data = useSomeAPI();
  return <Child data={data} />;
}
```

### External Store Subscription

```jsx
// 🔴 Manual subscription
useEffect(() => {
  const update = () => setIsOnline(navigator.onLine);
  window.addEventListener('online', update);
  window.addEventListener('offline', update);
  return () => { /* cleanup */ };
}, []);

// ✅ useSyncExternalStore
const isOnline = useSyncExternalStore(
  subscribe,
  () => navigator.onLine,
  () => true
);
```

## When Effects ARE Appropriate

- Synchronizing with external systems (non-React widgets, browser APIs)
- Analytics on component display
- Data fetching (with cleanup for race conditions)

### Data Fetching Pattern

```jsx
useEffect(() => {
  let ignore = false;
  fetchResults(query).then(json => {
    if (!ignore) setResults(json);
  });
  return () => { ignore = true; };
}, [query]);
```

Consider framework data fetching or libraries (React Query, SWR) for production.

## Key Question

**Why does this code need to run?**

- Because component was **displayed** → Effect
- Because user **did something** → Event handler
- To **derive/transform data** → Calculate during render

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seanwash) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

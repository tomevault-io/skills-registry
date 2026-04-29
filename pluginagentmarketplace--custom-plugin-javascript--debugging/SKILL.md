---
name: debugging
description: JavaScript debugging techniques using DevTools, Node.js debugger, and advanced troubleshooting. Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# JavaScript Debugging Skill

## Quick Reference Card

### Console Methods
```javascript
console.log('Basic log');
console.info('Info');
console.warn('Warning');
console.error('Error');

// Formatted output
console.table([{ a: 1 }, { a: 2 }]);
console.dir(object, { depth: null });

// Grouping
console.group('Group');
console.log('Inside group');
console.groupEnd();

// Timing
console.time('operation');
doSomething();
console.timeEnd('operation');

// Assertions
console.assert(condition, 'Failed message');

// Counting
console.count('label'); // label: 1, 2, 3...
```

### Debugger
```javascript
function processData(data) {
  debugger; // Pause here in DevTools
  const result = transform(data);
  return result;
}
```

### DevTools Breakpoints

| Type | Use Case |
|------|----------|
| Line | Pause at specific line |
| Conditional | Pause when condition true |
| DOM | Pause when DOM changes |
| XHR/Fetch | Pause on network requests |
| Event | Pause on event listeners |
| Exception | Pause on errors |

### Node.js Debugging
```bash
# Start with inspector
node --inspect app.js
node --inspect-brk app.js  # Break at start

# Chrome: chrome://inspect
# VS Code: Use launch.json
```

### VS Code launch.json
```json
{
  "version": "0.2.0",
  "configurations": [{
    "type": "node",
    "request": "launch",
    "name": "Debug",
    "program": "${workspaceFolder}/src/index.js"
  }]
}
```

## Troubleshooting Patterns

### Async Debugging
```javascript
async function debug() {
  console.log('1. Start');
  try {
    const result = await asyncOperation();
    console.log('2. Result:', result);
  } catch (error) {
    console.error('Error:', error);
    console.log('Stack:', error.stack);
  }
  console.log('3. End');
}
```

### Event Debugging
```javascript
// Log all events
element.addEventListener('click', (e) => {
  console.log('Event:', e.type);
  console.log('Target:', e.target);
  console.log('CurrentTarget:', e.currentTarget);
  console.log('Phase:', e.eventPhase);
});

// Monitor events (DevTools)
// monitorEvents(element, 'click')
```

### Memory Debugging
```javascript
// Check memory usage
if (performance.memory) {
  console.log('Heap:', performance.memory.usedJSHeapSize);
}

// Force garbage collection (DevTools)
// gc()
```

### Network Debugging
```javascript
// Log fetch requests
const originalFetch = window.fetch;
window.fetch = async (...args) => {
  console.log('Fetch:', args[0]);
  const response = await originalFetch(...args);
  console.log('Response:', response.status);
  return response;
};
```

## Common Issues

| Problem | Debug Approach |
|---------|----------------|
| undefined | Log typeof, check chain |
| NaN | Log operands, check types |
| Race condition | Add timestamps, sequence logs |
| Memory leak | Heap snapshot, check listeners |
| Event not firing | Log event, check delegation |

### Debug Checklist

1. **Reproduce** - Isolate the issue
2. **Inspect** - Check values and types
3. **Trace** - Follow execution flow
4. **Compare** - Working vs broken state
5. **Fix** - Apply smallest change
6. **Verify** - Test the fix

## Production Patterns

### Error Tracking
```javascript
window.onerror = (msg, url, line, col, error) => {
  console.error('Error:', { msg, url, line, col });
  // Send to error tracking service
};

window.onunhandledrejection = (event) => {
  console.error('Unhandled rejection:', event.reason);
};
```

### Performance Profiling
```javascript
// Mark timing points
performance.mark('start');
doWork();
performance.mark('end');

performance.measure('work', 'start', 'end');
console.log(performance.getEntriesByName('work'));
```

## Related

- **Agent 08**: Testing & Quality (detailed learning)
- **Skill: testing**: Test debugging
- **Skill: performance**: Performance profiling

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

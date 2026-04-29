---
name: performance
description: JavaScript performance optimization and Web Vitals improvement techniques. Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# JavaScript Performance Skill

## Quick Reference Card

### Core Web Vitals

| Metric | Target | Measures |
|--------|--------|----------|
| LCP | < 2.5s | Largest content paint |
| INP | < 200ms | Interaction responsiveness |
| CLS | < 0.1 | Visual stability |

### Debounce & Throttle
```javascript
// Debounce - delay until pause
function debounce(fn, delay) {
  let timeoutId;
  return (...args) => {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => fn(...args), delay);
  };
}

// Throttle - limit frequency
function throttle(fn, interval) {
  let lastCall = 0;
  return (...args) => {
    const now = Date.now();
    if (now - lastCall >= interval) {
      lastCall = now;
      fn(...args);
    }
  };
}

// Usage
window.addEventListener('scroll', throttle(handleScroll, 100));
input.addEventListener('input', debounce(search, 300));
```

### DOM Performance
```javascript
// Batch DOM reads/writes
const heights = elements.map(el => el.offsetHeight); // Read all
elements.forEach((el, i) => el.style.height = heights[i] + 'px'); // Write all

// Use DocumentFragment
const fragment = document.createDocumentFragment();
items.forEach(item => {
  const li = document.createElement('li');
  li.textContent = item;
  fragment.appendChild(li);
});
list.appendChild(fragment);

// Use requestAnimationFrame
function animate() {
  // Update DOM
  requestAnimationFrame(animate);
}
requestAnimationFrame(animate);
```

### Code Splitting
```javascript
// Dynamic import
const module = await import('./heavy-module.js');

// React lazy
const HeavyComponent = React.lazy(() => import('./Heavy'));

<Suspense fallback={<Spinner />}>
  <HeavyComponent />
</Suspense>
```

### Memory Optimization
```javascript
// WeakMap for metadata (auto cleanup)
const metadata = new WeakMap();
metadata.set(element, { clicks: 0 });

// Remove event listeners
const controller = new AbortController();
el.addEventListener('click', handler, { signal: controller.signal });
controller.abort(); // Cleanup

// Clear references
let heavyData = loadData();
// When done:
heavyData = null;
```

### Web Workers
```javascript
// main.js
const worker = new Worker('worker.js');
worker.postMessage({ data: largeArray });
worker.onmessage = (e) => console.log('Result:', e.data);

// worker.js
self.onmessage = (e) => {
  const result = processData(e.data);
  self.postMessage(result);
};
```

## Profiling

### Performance API
```javascript
// Mark and measure
performance.mark('start');
doWork();
performance.mark('end');
performance.measure('work', 'start', 'end');

const [measure] = performance.getEntriesByName('work');
console.log('Duration:', measure.duration);

// Resource timing
const resources = performance.getEntriesByType('resource');
resources.forEach(r => {
  console.log(r.name, r.duration);
});
```

### Console Timing
```javascript
console.time('operation');
await doSomething();
console.timeEnd('operation');
```

## Troubleshooting

### Common Issues

| Problem | Symptom | Fix |
|---------|---------|-----|
| Jank | Stuttering scroll | Use throttle, optimize DOM |
| Memory leak | Growing memory | Clear refs, remove listeners |
| Long task | UI freeze | Split work, use workers |
| Large bundle | Slow load | Code split, tree shake |

### Debug Checklist
```javascript
// 1. Profile in DevTools Performance panel
// 2. Check for long tasks (> 50ms)
// 3. Analyze bundle with webpack-bundle-analyzer
// 4. Run Lighthouse audit
// 5. Monitor with web-vitals library
```

## Production Patterns

### Lazy Loading Images
```javascript
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      entry.target.src = entry.target.dataset.src;
      observer.unobserve(entry.target);
    }
  });
});

document.querySelectorAll('img[data-src]').forEach(img => {
  observer.observe(img);
});
```

### Virtual Scrolling
```javascript
// Only render visible items
function renderVisibleItems(scrollTop, containerHeight) {
  const startIndex = Math.floor(scrollTop / itemHeight);
  const endIndex = startIndex + Math.ceil(containerHeight / itemHeight);
  return items.slice(startIndex, endIndex);
}
```

## Related

- **Agent 08**: Testing & Quality (detailed learning)
- **Skill: debugging**: Performance debugging
- **Skill: ecosystem**: Build optimization

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->

---
name: jquery4-modern
description: Modern jQuery 4.0 coding standards and migration guide. This skill should be used when writing, reviewing, or migrating jQuery code to ensure compatibility with jQuery 4.0+ and adherence to modern best practices. Covers deprecated API replacements, event handling patterns, AJAX with async/await, performance optimization, and TypeScript integration. Use when this capability is needed.
metadata:
  author: yunosukeyoshino
---

# jQuery 4.0 Modern Coding Skill

This skill provides guidance for writing modern jQuery code compatible with jQuery 4.0.0+ (released January 2026).

## When to Use This Skill

- Writing new jQuery code in projects using jQuery 4.0+
- Migrating legacy jQuery code from versions 1.x-3.x to 4.0
- Reviewing jQuery code for deprecated API usage
- Optimizing jQuery performance
- Integrating jQuery with TypeScript

## Critical: Banned APIs in jQuery 4.0

The following APIs were removed in jQuery 4.0 and must never be used. Replace with native JavaScript equivalents:

| Banned API       | Replacement                |
| ---------------- | -------------------------- |
| `$.isArray()`    | `Array.isArray()`          |
| `$.parseJSON()`  | `JSON.parse()`             |
| `$.trim()`       | `str.trim()`               |
| `$.type()`       | `typeof` / `instanceof`    |
| `$.now()`        | `Date.now()`               |
| `$.isNumeric()`  | `Number.isFinite()`        |
| `$.isFunction()` | `typeof fn === 'function'` |
| `$.isWindow()`   | `obj === window`           |
| `$.camelCase()`  | Custom function or lodash  |
| `$.nodeName()`   | `element.nodeName`         |

Also removed from prototype: `$elems.push()`, `$elems.sort()`, `$elems.splice()` — use `[].method.call($elems, ...)` instead.

## Core Coding Patterns

### Selector Best Practices

```javascript
// ✅ Correct: Cache selectors, use $ prefix for jQuery objects
const $container = $('#container')
const $items = $container.find('.item')

// ❌ Wrong: Repeated selector calls, element type in class selector
$('div.container').addClass('active')
$('div.container').find('.item') // Re-queries DOM
```

### Event Handling

```javascript
// ✅ Correct: Use .on() with event delegation
$container.on('click', '.item', (e) => {
	e.preventDefault()
	console.log($(e.currentTarget).data('id'))
})

// ✅ Correct: DOMReady shorthand with arrow function
$(() => {
	initApp()
})

// ❌ Wrong: Deprecated event methods
$element.click(handler) // Use .on("click", handler)
$element.bind('click', handler) // Use .on()
$(document).ready(function () {}) // Use $(() => {})
```

### AJAX with async/await

```javascript
// ✅ Correct: async/await pattern
async function fetchData(endpoint) {
	try {
		const data = await $.ajax({
			url: endpoint,
			method: 'GET',
			dataType: 'json',
		})
		return data
	} catch (error) {
		console.error('API Error:', error)
		throw error
	}
}

// ❌ Wrong: JSONP (security risk, auto-promotion removed in 4.0)
$.ajax({ url: '/api', dataType: 'jsonp' }) // Use CORS instead
```

### DOM Manipulation

```javascript
// ✅ Correct: Batch operations
const html = items.map((item) => `<li>${item.name}</li>`).join('')
$list.append(html)

// ✅ Correct: Use prop() for boolean attributes
$checkbox.prop('checked', true)
$input.prop('disabled', false)

// ❌ Wrong: Loop-based append (causes reflow per iteration)
items.forEach((item) => $list.append(`<li>${item.name}</li>`))
```

### Animation

Prefer CSS transitions with class manipulation over jQuery animations:

```javascript
// ✅ Correct: CSS class-based animation
$element.addClass('fade-in')
// CSS: .fade-in { transition: opacity 0.3s; opacity: 1; }

// ⚠️ Use jQuery animation only for dynamic values
$element.animate({ scrollTop: position }, 300)
```

## ES Modules Import

jQuery 4.0 supports ES modules natively:

```javascript
import $ from 'jquery'
```

## Detailed References

For comprehensive API migration tables, performance patterns, and TypeScript integration, see:

- `references/api-migration.md` - Complete deprecated API replacement guide
- `references/patterns.md` - Modern coding patterns with examples

## Code Review Checklist

When reviewing jQuery code, verify:

1. **No banned APIs** - Check for $.isArray, $.trim, $.parseJSON, etc.
2. **Cached selectors** - Same selector should not be called multiple times
3. **Event delegation** - Use .on() with delegation for dynamic elements
4. **Async/await for AJAX** - Avoid callback-style $.ajax
5. **Native JS utilities** - Prefer Array.isArray(), JSON.parse(), etc.
6. **CSS animations** - Prefer class-based transitions over .animate()

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yunosukeyoshino) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

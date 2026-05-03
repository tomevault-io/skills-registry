---
name: wordpress-media-modal
description: | Use when this capability is needed.
metadata:
  author: webzunft
---

# WordPress Media Modal (`wp.media.view.Modal`)

## When to Use This Skill

Use this skill when you need to:
- Create a modal/dialog in WordPress admin that has more content or interaction needs than an `alert()` in JavaScript
- Display a popup form for bulk editing
- Show complex confirmation dialogs or data entry forms

**Do NOT use for:**
- Frontend modals
- Block editor modals (use `@wordpress/components`)
- Simple alerts (use native `confirm()` or `alert()`)

## Quick Start

See complete working examples in `examples/` folder:
- **PHP Setup**: `examples/php/Modal_Handler.php`
- **JavaScript**: `examples/js/modal.js`
- **Template**: `examples/templates/modal-template.php`
- **CSS**: `examples/css/modal.css`. Additional CSS is only needed for custom elements. The modal itself doesn’t need new CSS to work out of the box.

## Key Principles

### 1. Always Use WordPress Native Components
- ✅ Use `wp.media.view.Modal` for consistency with WordPress UI
- ❌ Don't create custom modal HTML/CSS from scratch
- **Why:** Built-in accessibility, less maintenance, familiar UX

### 2. Proper Script Dependencies
```php
wp_enqueue_media(); // Loads wp.media, Backbone, Underscore
wp_enqueue_script(
    'your-modal-script',
    'path/to/modal.js',
    [ 'jquery', 'media-views' ], // Required dependencies
    VERSION,
    true
);
```

### 3. Critical Implementation Order

**This order MUST be followed:**

```javascript
// 1. Create controller with required methods
const modalController = _.extend({}, Backbone.Events, {
    state: function() { return this; },
    get: function() { return null; }
});

// 2. Create modal
const modal = new wp.media.view.Modal({ controller: modalController });

// 3. Open modal FIRST
modal.open();

// 4. THEN inject content
const content = wp.template('your-modal')({ data: 'value' });
modal.$el.find('.media-modal-content').html(content);

// 5. Attach event handlers
modal.$el.find('#save-button').on('click', saveHandler);

// 6. Cleanup on close
modal.on('close', function() { modal.remove(); });
```

**⚠️ Common Mistake:** Injecting content before `modal.open()` results in empty modal.

## Common Pitfalls & Solutions

### ❌ Pitfall: Empty Modal Content
**Error:** Modal opens but shows no content

**Cause:** Content injected before `modal.open()`

**Solution:**
```javascript
// ❌ WRONG ORDER
modal.$el.find('.media-modal-content').html(content);
modal.open(); // Too late!

// ✅ CORRECT ORDER
modal.open(); // First!
modal.$el.find('.media-modal-content').html(content); // Then inject
```

---

### ❌ Pitfall: "this.controller.state is not a function"
**Cause:** Controller missing required methods

**Solution:**
```javascript
// ✅ Must include both state() and get()
const modalController = _.extend({}, Backbone.Events, {
    state: function() { return this; },
    get: function() { return null; }
});
```

---

### ❌ Pitfall: Modal Closes Before AJAX Completes
**Cause:** Not waiting for async operations

**Solution:** Use callback pattern
```javascript
modal.$el.find('#save').on('click', function() {
    performAsyncSave(function onComplete() {
        modal.close(); // Only close when done
    });
});
```

See `examples/js/modal.js` for complete AJAX tracking implementation.

---

Full implementation in `examples/js/modal.js`.

---

### ❌ Pitfall: Using wp.media.view.Toolbar
**Cause:** Toolbar is for complex media frames, not simple modals

**Solution:** Use plain HTML buttons in template
```html
<!-- ✅ CORRECT: Buttons in template content -->
<div class="modal-buttons">
    <button class="button button-primary" id="save">Save</button>
    <button class="button" id="cancel">Cancel</button>
</div>
```

See `examples/templates/modal-template.php`.

## Best Practices

### 1. Button State Management
Always disable buttons during async operations:

```javascript
$saveButton.prop('disabled', true).text('Saving...');
$cancelButton.prop('disabled', true);
$spinner.css('visibility', 'visible');

performSave(function() {
    modal.close(); // Re-enable happens automatically on close
});
```

### 2. Only Update Changed Values
Avoid unnecessary AJAX requests:

```javascript
if (currentValue !== newValue) {
    element.value = newValue;
    element.dispatchEvent(new Event('change'));
}
```

### 3. Conditional Content with Underscore Templates
```html
<# if ( data.showWarning ) { #>
    <div class="warning">Warning message</div>
<# } #>
```

### 4. Always Clean Up
```javascript
modal.on('close', function() {
    modal.remove(); // Prevents stale data on next open
});
```

## File Structure for Your Plugin

```
your-plugin/
├── admin/
│   ├── includes/
│   │   └── class-modal-handler.php  (See examples/php/)
│   └── assets/
│       ├── js/
│       │   └── modal.js             (See examples/js/)
│       └── css/
│           └── modal.css            (See examples/css/)
└── templates/
    └── modal-template.php           (See examples/templates/)
```

## Debugging Checklist

When modal doesn't work, verify:

1. `wp_enqueue_media()` called?
2. Dependencies `['jquery', 'media-views']` included?
3. Controller has `state()` and `get()` methods?
4. `modal.open()` called BEFORE content injection?
5. Template ID matches (`tmpl-name` → `wp.template('name')`)?
6. Event handlers attached AFTER content injection?
7. `modal.remove()` called on close?
8. Browser console shows no JavaScript errors?
9. Content actually injected into `.media-modal-content`?

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/webzunft) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

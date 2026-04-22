---
name: livewire-development
description: Develops Livewire 4 components. Activates for `wire:` directives, reactivity, islands, or component changes. Use when this capability is needed.
metadata:
  author: jdecode
---

# Livewire Development
**Docs**: `search-docs` for Livewire 4.

## Creation
- SFC (Default): `php artisan make:livewire create-post`
- Multi-file: `... --mfc`
- Class-based: `... --class`
- Convert: `php artisan livewire:convert name`

## Livewire 4 Differences
- Full-page: `Route::livewire()`. Config: `component_layout`.
- `wire:model` ignores child events (use `.deep` for old behavior).
- Tags must be closed.
- JS: `$wire.$js.name = fn`, `interceptMessage`.
- Alpine bundled.

## Features
- **Islands**: `@island(name: 'x')` for isolation.
- **Async**: `wire:click.async`.
- **Directives**: `wire:sort`, `wire:intersect`, `wire:ref`, `.renderless`, `.preserve-scroll`.
- **Loading**: `wire:loading`, `data-loading` attr added automatically.

<code-snippet name="SFC Example" lang="php">
<?php
use Livewire\Component;
new class extends Component {
    public int $count = 0;
    public function increment() { $this->count++; }
}; ?>
<div><button wire:click="increment">{{ $count }}</button></div>
</code-snippet>

<code-snippet name="Test" lang="php">
Livewire::test(Counter::class)->call('inc')->assertSet('count', 1);
</code-snippet>

## Best Practices
- `wire:key` in loops (mandatory).
- `wire:model.live` for realtime.
- Validate in actions.
- Debug via Network tab/Console

# Livewire 4 JavaScript Integration

## Interceptor System (v4)

### Intercept Messages

```js
Livewire.interceptMessage(({ component, message, onFinish, onSuccess, onError }) => {
    onFinish(() => { /* After response, before processing */ });
    onSuccess(({ payload }) => { /* payload.snapshot, payload.effects */ });
    onError(() => { /* Server errors */ });
});
```

### Intercept Requests

```js
Livewire.interceptRequest(({ request, onResponse, onSuccess, onError, onFailure }) => {
    onResponse(({ response }) => { /* When received */ });
    onSuccess(({ response, responseJson }) => { /* Success */ });
    onError(({ response, responseBody, preventDefault }) => { /* 4xx/5xx */ });
    onFailure(({ error }) => { /* Network failures */ });
});
```

### Component-Scoped Interceptors

```blade
<script>
    this.$intercept('save', ({ component, onSuccess }) => {
        onSuccess(() => console.log('Saved!'));
    });
</script>
```

## Magic Properties

- `$errors` - Access validation errors from JavaScript
- `$intercept` - Component-scoped interceptors

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jdecode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

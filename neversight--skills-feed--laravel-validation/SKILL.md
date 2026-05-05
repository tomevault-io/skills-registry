---
name: laravel-validation
description: Form request validation and comprehensive validation testing. Use when working with validation rules, form requests, validation testing, or when user mentions validation, form requests, validation rules, conditional validation, validation testing. Use when this capability is needed.
metadata:
  author: neversight
---

# Laravel Validation

Form requests as single source of truth for validation, with comprehensive testing patterns.

## Core Concepts

**[form-requests.md](references/form-requests.md)** - Validation rules:
- Array-based validation rules
- Custom validation rules
- Conditional validation
- Custom error messages
- DTO transformation via `toDto()`

**[validation-testing.md](references/validation-testing.md)** - Validation testing:
- RequestDataProviderItem helper
- Pest datasets for systematic testing
- Built-in helper methods (string, email, number, date, array, boolean)
- Nested array testing
- Conditional validation testing
- Real-world examples

## Pattern

```php
final class CreateOrderRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'user_id' => ['required', 'integer', 'exists:users,id'],
            'items' => ['required', 'array', 'min:1'],
            'items.*.product_id' => ['required', 'integer'],
            'items.*.quantity' => ['required', 'integer', 'min:1'],
        ];
    }

    public function toDto(): CreateOrderDto
    {
        return CreateOrderDto::from($this->validated());
    }
}
```

All validation lives in form requests. Test validation systematically.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->

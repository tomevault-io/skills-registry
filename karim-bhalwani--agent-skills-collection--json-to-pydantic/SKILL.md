---
name: json-to-pydantic
description: Convert JSON data snippets into Python Pydantic data models with proper type annotations and validation. Use when converting JSON schemas to Pydantic models, generating data validation models from JSON examples, or creating Python data classes. Use when this capability is needed.
metadata:
  author: karim-bhalwani
---

# JSON to Pydantic Skill

This skill helps convert raw JSON data or API responses into structured, strongly-typed Python classes using Pydantic.

## Instructions

1. **Analyze the Input**: Look at the JSON object provided by the user.
2. **Infer Types**:
   - `string` -> `str`
   - `number` -> `int` or `float`
   - `boolean` -> `bool`
   - `array` -> `List[Type]`
   - `null` -> `Optional[Type]`
   - Nested Objects -> Create a separate sub-class.

3. **Follow the Example**:
   Review `examples/` to see how to structure the output code. notice how nested dictionaries like `preferences` are extracted into their own class.

   - Input: `examples/input_data.json`
   - Output: `examples/output_model.py`

## Style Guidelines

- Use `PascalCase` for class names.
- Use type hints (`List`, `Optional`) from `typing` module.
- If a field can be missing or null, default it to `None`.

## Outputs & Deliverables

- **Primary Output**: Python Pydantic models with complete type annotations
- **Secondary Output**: Validation rules and field defaults
- **Success Criteria**: Models match JSON structure and include proper type hints
- **Quality Gate**: Ready for integration into implementer's codebase

## Constraints

- **NO business logic.** Data models only.
- **NO implementation code** beyond model definitions.
- Must handle nested objects and optional fields correctly.

## Common Pitfalls

- **Ignoring Nested Structures**: Not extracting nested objects into separate classes creates monolithic models. Always decompose; create sub-classes for nested dicts.
- **Wrong Type Inference**: Confusing `null` with missing fields. `null` = `Optional`, missing entirely = Field with `default_factory`. Be precise.
- **Generic Field Names**: Using `data`, `value`, `result` instead of domain-specific names. Use the field name from JSON.
- **Skipping Validation**: Not adding constraints like `Field(min_length=1)` or regex patterns. Add validation rules to the model.
- **Mixing Array Element Types**: Not using `Union` types when arrays can contain multiple types. Use generics correctly.
- **Missing Aliases**: Not mapping JSON camelCase to Python snake_case with Field aliases. Handle naming convention mismatches explicitly.

## Integration Points

| Phase | Input From | Output To | Context |
|-------|-----------|-----------|---------|
| Input | JSON response or API data | Model generation | Analyze JSON structure and infer types |
| Decomposition | Nested structures | Sub-class extraction | Create separate Pydantic models for objects |
| Integration | Generated models | `implementer` | Use in service layer for validation |
| Validation | Model with constraints | Runtime protection | Pydantic validates all incoming data |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/karim-bhalwani) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

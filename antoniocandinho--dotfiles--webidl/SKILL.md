---
name: webidl
description: Use and interpret Web IDL with quick, accurate reference habits. Use when this capability is needed.
metadata:
  author: antoniocandinho
---

## What I do

- Answer Web IDL questions using the cheat sheet in this skill first.
- Cite spec anchors when summarizing rules or grammar.
- Fetch the spec only when the cheat sheet is missing details.

## Default workflow

- Read the inline cheat sheet before answering.
- Prefer the local snapshot `skills/webidl/spec.md` for details.
- Avoid full-spec reads; when consulting the spec, search anchors and read 50-200 lines around the match.
- If anything is unclear, reference https://webidl.spec.whatwg.org/ with anchors.

## Grammar essentials (compact)

- Definitions: `#index-prod-Definitions`, `#index-prod-Definition`
- Interface: `#index-prod-InterfaceRest`, `#index-prod-InterfaceMembers`
- Members: `#index-prod-Operation`, `#index-prod-AttributeRest`
- Dictionary/Enum/Typedef: `#index-prod-Dictionary`, `#index-prod-Enum`, `#index-prod-Typedef`
- Types: `#index-prod-Type`, `#index-prod-UnionType`
- Extended attributes: `#index-prod-ExtendedAttributeList`

## Cheat sheet

Last verified: 2026-01-11 (spec header)
Local snapshot: `skills/webidl/spec.md`
Spec: https://webidl.spec.whatwg.org/

### Core structure

- IDL fragments are unordered; references can appear across fragments. Spec: https://webidl.spec.whatwg.org/#idl
- Definitions include interfaces, mixins, namespaces, dictionaries, enums, callbacks, typedefs, includes. Spec: https://webidl.spec.whatwg.org/#idl
- Extended attributes can annotate definitions and members. Spec: https://webidl.spec.whatwg.org/#idl-extended-attributes

### Identifiers

- A leading underscore escapes identifiers; it is removed to obtain the IDL identifier. Spec: https://webidl.spec.whatwg.org/#idl-names
- Reserved identifiers: `constructor`, `toString`, and any identifier beginning with `_`. Spec: https://webidl.spec.whatwg.org/#idl-names
- `toJSON` is only for regular operations that convert to JSON types. Spec: https://webidl.spec.whatwg.org/#idl-tojson-operation

### Interfaces and inheritance

- Interfaces can inherit from a single interface; cycles are invalid. Spec: https://webidl.spec.whatwg.org/#idl-interfaces
- Partial interfaces merge members into the base interface. Spec: https://webidl.spec.whatwg.org/#idl-interfaces
- Includes statements add mixin members to host interfaces. Spec: https://webidl.spec.whatwg.org/#includes-statement

### Interface mixins

- Mixins are for sharing attributes/operations across multiple interfaces. Spec: https://webidl.spec.whatwg.org/#idl-interface-mixins
- Mixins cannot contain static/special operations, iterable, maplike, or setlike. Spec: https://webidl.spec.whatwg.org/#idl-interface-mixins
- Partial mixins merge members into the mixin. Spec: https://webidl.spec.whatwg.org/#idl-interface-mixins

### Callbacks

- Callback interfaces can be implemented by plain objects. Spec: https://webidl.spec.whatwg.org/#idl-callback-interfaces
- Callback functions are separate from callback interfaces. Spec: https://webidl.spec.whatwg.org/#idl-callback-functions

### Types (entry points)

- Core type definitions and categories: https://webidl.spec.whatwg.org/#idl-types
- Union types: https://webidl.spec.whatwg.org/#idl-union
- Nullable types: https://webidl.spec.whatwg.org/#idl-nullable-type
- Sequences/records/promises: https://webidl.spec.whatwg.org/#idl-sequence https://webidl.spec.whatwg.org/#idl-record https://webidl.spec.whatwg.org/#idl-promise

### Overloading

- Overloading rules and vs. union discussion: https://webidl.spec.whatwg.org/#idl-overloading https://webidl.spec.whatwg.org/#idl-overloading-vs-union

## References

- Local snapshot: `skills/webidl/spec.md`
- Spec: https://webidl.spec.whatwg.org/
- Spec grammar section: https://webidl.spec.whatwg.org/#idl-grammar

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/antoniocandinho) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

---
name: writing-roc-lang
description: Writes Roc code with strong static types, helpful compiler errors, and functional programming. Use when the user wants Roc code, mentions Roc, functional programming with types, or needs .roc files. Covers both full applications and one-off Roc scripts with shebangs. Use when this capability is needed.
metadata:
  author: jeninh
---

Write code in Roc, a functional language with strong types, helpful compiler errors, and no runtime. Compiles to standalone binaries.

## Reference documentation

- **Language guide**: See [llms.md](references/llms.md) for comprehensive tutorial
- **Standard library**: See [builtins-llms.md](references/builtins-llms.md) for all builtin modules
- **CLI scripting**: See [scripting.md](references/scripting.md) for executable scripts with shebang

## Language fundamentals

### Functions

```roc
# Lambda syntax
add = |a, b| a + b

# Multi-line with indentation
process = |text|
    trimmed = Str.trim(text)
    Str.to_upper(trimmed)

# Type signatures (optional but helpful)
greet : Str -> Str
greet = |name| "Hello, ${name}!"
```

### Pattern matching

```roc
when value is
    Ok(content) -> process(content)
    Err(NotFound) -> "not found"
    Err(_) -> "other error"
```

### Records and tags

```roc
# Records
user = { name: "Alice", age: 30 }
user.name  # Access field

# Tags (algebraic data types)
Color : [Red, Green, Blue, Custom Str]
my_color = Custom("purple")
```

## Error handling

Roc uses `Result` types, not exceptions. Use `?` to short-circuit:

```roc
main! = |_args|
    content = File.read_utf8!(path)?  # Returns early if error
    Stdout.line!("Success!")
```

Or explicit pattern matching:

```roc
when result is
    Ok(content) -> Stdout.line!("Read: ${content}")
    Err(err) -> Err(Exit(1, "Failed: ${Inspect.to_str(err)}"))
```

## Common patterns

### Working with lists

```roc
numbers = [1, 2, 3, 4, 5]
evens = List.keep_if(numbers, |n| n % 2 == 0)
doubled = List.map(evens, |n| n * 2)
sum = List.walk(doubled, 0, Num.add)
```

### String manipulation

```roc
text = Str.concat("Hello", " World")
words = Str.split(text, " ")
trimmed = Str.trim("  spaces  ")
replaced = Str.replace_each(text, "World", "Roc")
```

## Code quality tools

```bash
roc format script.roc  # Auto-format code
roc check script.roc   # Type check without running
roc test script.roc    # Run expect expressions
```

### Tests with expect

```roc
add = |a, b| a + b

expect add(2, 3) == 5
expect add(0, 0) == 0
```

## Key concepts

- **Immutable**: All values immutable; operations return new values
- **Type inference**: Types inferred but signatures help clarity
- **Helpful errors**: Compiler provides detailed, friendly messages
- **No exceptions**: Use `Result` for errors; no try/catch
- **No runtime**: Compiled binaries are standalone
- **Functional**: Functions are first-class; use `|param| expression` syntax

## Maturity caveat

Roc is pre-1.0. Core features work well, but expect:
- Breaking changes between versions
- Smaller community/fewer examples
- Excellent for learning; use production caution

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeninh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

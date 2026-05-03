---
name: qti
description: Generate a quiz or test for an LMS in QTI format Use when this capability is needed.
metadata:
  author: jncraton
---

Only use multiple choice questions with four possible answers. A variety of questions should be provided focusing different levels of Bloom's revised taxonomy (remember, understand, apply, analyze, evaluate, create).

## Steps

Two files are created based on the base name provided by the user. They must be generated in the following order:

1. Directly generate the json for a quiz as the user-supplied filename with a .json extension.
2. Build the qti zip file for the quiz using the following shell command: `pipx run json2qti {quiz json from step 1}`

## Text File Format

The output json file should be in this format.

1.  **Quiz Title:** The top-level key.
2.  **Questions:** Keys inside the object.
3.  **Answers:** A list of strings. **The first answer is always the correct one.**

```json
{
  "Basic Math Quiz": {
    "What is 1+1?": ["2", "3", "4", "5"],
    "What is 1+2?": ["3", "4", "5", "6"]
  }
}
```

You can include code snippets using markdown-style syntax:

- **Inline Code:** Wrap text in single backticks (\`).
- **Block Code:** Wrap text in triple backticks (\`\`\`).

````json
{
  "Python Quiz": {
    "What does `print('hello')` output?": ["`hello` to stdout", "`hello` to stderr", "Nothing"],
    "What does this function do?\n```\ndef add(a, b):\n    return a + b\n```": [
      "Returns the sum of two numbers",
      "Returns the product of two numbers",
      "Prints the numbers"
    ]
  }
}
````

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jncraton) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

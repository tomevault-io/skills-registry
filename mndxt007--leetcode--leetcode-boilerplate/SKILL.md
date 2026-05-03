---
name: leetcode-boilerplate
description: Create boilerplate C# script (.csx) files for LeetCode problems. Use when the user provides a LeetCode problem URL, problem number, or pastes a problem description and wants a scaffolded .csx file with the problem description as a comment, method stub, test cases, and test runner. Does NOT solve the problem. The file is placed in the correct category folder with proper naming convention. Use when this capability is needed.
metadata:
  author: mndxt007
---

# LeetCode Boilerplate Generator

Generate scaffolded .csx files for LeetCode problems in the repository at `D:\DS\Code`.

## Workflow

1. **Get problem details** from the user input (URL, pasted description, or problem number)
2. **Determine file placement** (category folder + file name)
3. **Generate the .csx boilerplate** file
4. **Confirm** the created file path

## Step 1: Get Problem Details

**If given a LeetCode URL**: Use `web_fetch` to retrieve the problem page content. Extract the problem title, number, description, examples, and constraints.

**If given a pasted description**: Parse the provided text directly for the problem title, number, examples, and constraints.

**If given just a problem number or name**: Ask the user to provide the full problem description or URL.

## Step 2: Determine File Placement

Read [references/categories.md](references/categories.md) for the full folder mapping and file naming rules.

**Ask the user** which category folder to place the file in. Present choices from the category list. If the problem clearly maps to one category, suggest it as the recommended choice.

**File name format**: `{LeetCodeNumber}_{Problem_Name_With_Underscores}.csx`

Before creating, check if the file already exists using `glob`. If it does, inform the user and ask how to proceed.

## Step 3: Generate the .csx Boilerplate

Read [references/examples.md](references/examples.md) for complete code examples showing the exact output format for different problem types.

### Output Structure

```
/*
{Problem description as-is with proper newline formatting}
*/


// TODO: Implement solution
{Method stub with throw new NotImplementedException()}


{Test case list definition}

{foreach loop running each test case and printing results}
```

### Critical Rules

- **NEVER provide any solution code, hints, algorithm suggestions, or implementation guidance**
- Method stub body must only contain `throw new NotImplementedException();`
- Copy the problem description verbatim into the `/* */` comment block
- Extract test cases from the problem's examples
- Use C# 12 collection expressions `[...]` for test case lists
- Use value tuples for multi-parameter test cases
- Print format: `Console.WriteLine($"Testcase: param-{val}")` then `Console.WriteLine($"MethodName - {result}")`
- For array output use `String.Join(',', array)`, wrapping in `[{...}]`
- For problems requiring custom data structures (ListNode, TreeNode, etc.), include the class definition and helper methods (LoadList, PrintList, etc.) between the comment block and the method stub

### Method Signature

Use the exact method signature from LeetCode. Common patterns:
- `public int[] TwoSum(int[] nums, int target)`
- `public bool WordBreak(string s, IList<string> wordDict)`
- `public IList<IList<int>> Permute(int[] nums)`

For `IList<T>` parameters, use concrete arrays/lists in test case data.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mndxt007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

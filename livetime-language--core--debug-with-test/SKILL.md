---
name: debug-with-test
description: Fix a bug with a given, failing unit test Use when this capability is needed.
metadata:
  author: livetime-language
---
# Find and Fix a Problem

1. Use vscode's built-in test-runner tool execute/runTests or the runTests mcp server to run the unit tests and analyze the output carefully.

2. Analyze the code carefully to find all possible causes of the problem.

3. Think hard to create an extensive list of hypotheses of all possible causes of the problem.

4. Important: Output all your hypotheses!

5. Add extensive print statements to the code that help you confirm or disprove your hypotheses. For example:

Player
	tick
		positon += direction
		print "{this} moved in {direction} to {position}"

		let cell = getCellAt position
		print "{this} landed on cell type {cell?.type} at position {cell?.gridPos}"

6. Run the unit test again and carefully analyze the output of your print statements. The goal is to confirm one of your hypotheses. Repeat steps 1-6 until you can confirm one of your hypotheses.

7. Once you confirmed a hypothesis, fix the problem.

8. Repeat until the given unit test is passing.

9. Remove all the print statements you added.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/livetime-language) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->

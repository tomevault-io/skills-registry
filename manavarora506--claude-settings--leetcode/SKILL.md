---
name: leetcode
description: Deep LeetCode practice through Socratic questioning. Helps understand algorithm nuances, build mental models, and make fundamentals second nature. Use when practicing coding problems, studying algorithms, or preparing for interviews. Use when this capability is needed.
metadata:
  author: manavarora506
---

# LeetCode Deep Practice Skill

## Core Philosophy

1. **Spend a long time on simple things** - Master fundamentals before moving to hard problems
2. **Think of concepts in many ways** - Build your own mental representations
3. **Implement core tools from scratch** - Don't rely on library functions you don't understand
4. **Focus on nuances** - Binary search bounds, recursion base cases, and edge cases trip people up mid-interview
5. **Make fundamentals second nature** - So harder problems become tractable

## Two Main Modes

### 1. Fundamentals Mode

When the user wants to practice a pattern (e.g., "Let's practice binary search"):

1. **Start with exploration**: Ask them to walk through the concept in their own words
   - "Walk me through how you'd search a sorted array"
   - "What's the core insight that makes this efficient?"

2. **Identify gaps through questions** - Don't lecture. Ask questions that reveal understanding:
   - "What happens when the target isn't in the array?"
   - "Why do we use that specific loop condition?"

3. **Present multiple mental models** - Offer 2-3 different ways to think about the concept:
   - Binary search as "halving the search space"
   - Binary search as "maintaining an invariant about where the answer could be"
   - Binary search as "finding the boundary where a predicate flips"

4. **Have them implement from scratch** - No copy-paste, no looking up syntax

5. **Drill the nuances** - Cover the specific edge cases and variants that trip people up

6. **Build lasting intuition** - Connect to when/why this pattern applies

### 2. Problem Mode

When the user shares a specific problem they're working on:

1. **Don't give the answer immediately** - Resist the urge to solve it for them

2. **Ask what they've tried**:
   - "What approaches have you considered?"
   - "What made you think of that approach?"
   - "Where did you get stuck?"

3. **Guide toward the key insight** - Use questions to lead them:
   - "What would a brute force solution look like?"
   - "Is there repeated work we could eliminate?"
   - "What pattern does this remind you of?"

4. **Connect to fundamentals** - Link the problem to patterns they should know

5. **After solving** - Identify what fundamental was missing or weak

---

## Topics Covered

### Search & Sort

**Binary Search**
- Core: halving the search space on a sorted/monotonic sequence
- Bounds: `< vs <=`, when each is appropriate
- Mid calculation: rounding down vs up, why it matters
- Variants: leftmost occurrence, rightmost occurrence, first true in predicate
- When it applies: any monotonic property, not just sorted arrays

**Partition Logic**
- Quickselect for kth element
- Two-pointer partitioning
- Dutch national flag problem

### Graph Traversal

**BFS vs DFS**
- BFS: shortest path in unweighted graphs, level-order traversal
- DFS: existence checks, exhaustive search, topological sort
- Choosing between them based on the problem

**Implementation Details**
- Iterative vs recursive DFS (explicit stack vs call stack)
- Level tracking in BFS (queue size technique)
- When to mark visited (before adding to queue vs when popping)

**Cycle Detection**
- Undirected: visited set is sufficient
- Directed: need visited vs in-current-path distinction
- Using colors: white/gray/black

**Topological Sort**
- Kahn's algorithm (BFS with in-degree)
- DFS-based (reverse post-order)
- Detecting if a valid ordering exists

**Union-Find**
- Path compression
- Union by rank/size
- When to use vs BFS/DFS

### Recursion & Dynamic Programming

**Recursion Fundamentals**
- Base cases: when to stop, what to return
- State passing: what information flows down
- Return values: what information flows up
- Trusting the recursion (don't trace every call mentally)

**Backtracking**
- The three steps: choose, explore, unchoose
- Pruning: when to stop exploring a branch
- Generating permutations, combinations, subsets

**Memoization**
- What to cache: the result of expensive computations
- How to key: what parameters define a unique subproblem
- Recognizing overlapping subproblems

**Dynamic Programming**
- State definition (the hardest part): what information do we need to solve a subproblem?
- Recurrence relation: how do subproblems relate?
- Base cases: smallest subproblems with known answers
- Tabulation vs memoization: iterative vs recursive tradeoffs

### Data Structures

**Heaps**
- Min heap vs max heap: which to use when
- K-element problems: kth largest, k closest
- Two-heap pattern: median tracking

**Tries**
- Implementation: node structure, children representation
- When useful: prefix matching, autocomplete, word search

**Monotonic Stack/Queue**
- Mental model: maintaining a sorted structure as you scan
- Applications: next greater element, sliding window maximum

**Union-Find (Disjoint Set)**
- Path compression: flattening the tree on find
- Union by rank: keeping the tree balanced
- Applications: connected components, cycle detection in undirected graphs

---

## Socratic Question Progressions

### Binary Search

1. "Can you write binary search for finding a target in a sorted array?"
2. (After attempt) "What's your loop condition? Walk me through why you chose that."
3. "What happens if the target isn't in the array? What does your code return?"
4. "Now find the *leftmost* occurrence of a duplicate element. What changes?"
5. "When do you use `left = mid` vs `left = mid + 1`? What goes wrong if you choose incorrectly?"
6. "Can you apply binary search to a problem where you're not searching an array, but testing a predicate?"

### BFS/DFS

1. "When would you choose BFS over DFS, or vice versa?"
2. "Can you implement BFS for shortest path in an unweighted graph?"
3. "How do you track which level you're on during BFS?"
4. "When should you mark a node as visited - before adding to the queue, or when you pop it?"
5. "How would you detect a cycle in a directed graph vs an undirected graph?"

### Recursion

1. "What makes a good base case? How do you know you have all of them?"
2. "Walk me through how you'd generate all subsets of a set recursively."
3. "In backtracking, why do we need to 'unchoose'? What happens if we skip it?"
4. "How do you decide what state to pass down vs what to return up?"

### Dynamic Programming

1. "How do you identify that a problem has overlapping subproblems?"
2. "What's the hardest part of DP for you?" (Usually state definition)
3. "For this problem, what's the minimum state needed to define a subproblem?"
4. "Can you write the recurrence before coding?"
5. "How would you convert this memoized solution to tabulation?"

---

## Nuance Checklists

### Binary Search Nuances

- [ ] **Loop condition**: `while left < right` vs `while left <= right`
  - `left < right`: loop ends when left == right (one candidate)
  - `left <= right`: loop ends when left > right (no candidates)
- [ ] **Mid calculation**: `(left + right) // 2` vs `(left + right + 1) // 2`
  - Rounding down vs up matters when search space shrinks to 2 elements
- [ ] **Update rules**: `left = mid + 1` vs `left = mid`
  - If mid can still be the answer, use `left = mid`
  - If mid is definitely not the answer, use `left = mid + 1`
- [ ] **Return value**: What to return when element not found?
- [ ] **Overflow prevention**: Use `left + (right - left) // 2` in languages with fixed-size integers
- [ ] **Invariant**: What property holds throughout the search?

### BFS/DFS Nuances

- [ ] **When to mark visited**: Before adding to queue (prevents duplicates in queue) vs when popping (can lead to duplicates)
- [ ] **BFS for shortest path**: Only works for unweighted graphs
- [ ] **Cycle detection**: Directed graphs need visited + in-current-path; undirected just need visited
- [ ] **Space complexity**: Recursive DFS uses O(depth) stack space
- [ ] **Level tracking**: Use queue size at start of each level

### Recursion/Backtracking Nuances

- [ ] **Base cases**: All termination conditions covered?
- [ ] **State mutation**: Are you properly undoing changes when backtracking?
- [ ] **Pruning**: Are there branches you can skip early?
- [ ] **Avoiding duplicates**: How to handle duplicate elements in permutations/combinations?

### DP Nuances

- [ ] **State definition**: Is your state sufficient to solve the subproblem?
- [ ] **State minimization**: Are you using more state than necessary?
- [ ] **Initialization**: Are base cases correctly initialized in the table?
- [ ] **Iteration order**: For tabulation, are you processing subproblems before they're needed?
- [ ] **Space optimization**: Can you reduce from 2D to 1D array?

---

## Session Flow

When `/leetcode` is invoked:

1. **Ask what they want to practice**:
   - "What would you like to work on today? A specific problem you're stuck on, or deep practice on a fundamental pattern?"

2. **For Fundamentals Mode**:
   - Confirm the topic
   - Start with conceptual questions
   - Have them implement
   - Drill nuances with the checklist
   - Suggest follow-up problems to solidify

3. **For Problem Mode**:
   - Get the problem details
   - Ask what they've tried
   - Guide with questions, not answers
   - After solving, connect to fundamentals
   - Identify gaps to work on

4. **End with reflection**:
   - "What's one thing that's clearer now?"
   - "What would you like to practice next?"

---

## Key Principles for the Coach

- **Ask, don't tell** - Questions reveal understanding better than lectures
- **Embrace productive struggle** - Resist giving answers too quickly
- **Multiple representations** - Offer different mental models for the same concept
- **Connect patterns** - Help them see how problems relate to fundamentals
- **Focus on the nuances** - The details that trip people up are where mastery lives
- **Build confidence through competence** - Real confidence comes from actually understanding

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/manavarora506) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->

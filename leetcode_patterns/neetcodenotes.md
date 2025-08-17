# Algorithms & Data Structures — Clean Notes (Reorganized)

## Arrays & Hashing
- Generally about counting, grouping, matching, and existence checks.

### Prefix + Hashing
- When a subarray property can be written as `metric[j] - metric[i] = target`, use a **hash on the prefix metric**.
- Common metrics:
  - Prefix sum for sums (exact k, ≥k, max length, count, mode, etc.).
  - Prefix sum % k for divisibility or grouping by remainder.
- Used for **contiguous subarray** questions, but note:
  - With negatives, sum isn’t monotonic → **sliding window doesn’t apply**.
  - Often you must **count all valid subarrays**, not just find one “best” window.
- You can rewrite “`sum(nums[i..j])` has property P” as a **difference of prefix metrics**.

### Choosing the Technique (Quick Guide)
- Finding pairs/groups in a sorted context? → **Two Pointers**.
- Single best contiguous window with a **monotonic** constraint (all positives, etc.)? → **Sliding Window**.
- Counting/longest/shortest subarrays with a **sum/remainder** goal (often with negatives)? → **Prefix Sum + Hash**.

## Two Pointers
- Use when comparing/finding **pairs or groups** along **linear** structures.
- Data is often **sorted** so pointer moves have predictable effects that push you **closer to the goal**.
- Useful for **in-place reordering** via swaps.

## Sliding Window
- For comparing **substrings/subarrays** that must maintain a certain **invariant**.
- **Expand** while invariant holds; **shrink** when it breaks, until valid again.
- Track your **best (min/max/cost)** at each valid window.
- Typical pattern: right pointer = outer **for-loop**; left pointer = inner **while** (while invariant is broken).

## Stack
- Questions to ask:
  - Do I process elements **one by one**?
  - Does a new element’s fate depend on **recent survivors** (especially the most recent)?
  - Can **collisions/matches/removals cascade** (one removal exposes another)?
- Go-to for **next greater/smaller** patterns:
  - Pop while stack and `stack[-1]` isn’t greater/smaller, then push.
- Also go-to for **matching/nesting** (e.g., parentheses) and **expression evaluation**.
  - Matching = pairing items.
  - Nesting = pairs appear **inside each other** in layers.

## Binary Search
- Use `while l <= r:` and move pointers so the search space **always shrinks**.
- For **first/last** instance, keep a running **best answer** and return it at the end.

### Answer-Space Search (Parametric Search)
- Look for prompts like: “What’s the **best value V** so a **feasibility check** passes?”
- The feasibility check is usually **O(n)** (e.g., Koko eats bananas at speed `V`).
- It’s basically **binary search for the first feasible value**.
- For **rotated sorted arrays**, decide which half is **sorted** and whether the target lies within it.
- With **many duplicates**, you may have to **shrink both ends** (worst case O(n)).

## Linked List
- Recognizable; reason through pointer movement.
- Key techniques:
  1) **Dummy head** for edge cases (insert/delete at head).
  2) **Two pointers (fast/slow)**.
  3) **Find middle**, **detect cycles**, **k-th from end**.
  4) **Reversal** — 3-pointer (whole list) and 2-pointer with anchoring (sub-list).
  5) **Hashing to save nodes** (e.g., handling random/cross links).

## Trees
- **DFS** = recursion or stack; **BFS** = queue.
- In recursive tree problems (also in backtracking & graphs), **capture results of recursive calls** so you can make decisions later (helps with bubbling answers up).

### Traversals
- **Pre-order:** capture/build paths as you go down.
- **In-order:** mainly for BSTs when you need sorted order or range validation.
- **Post-order:** aggregate/combine results from both subtrees before handling parent.
- **Level-order:** BFS; use when you need level-by-level info or shortest path in an unweighted tree.

## Heap / Priority Queue
- Use when you must **continuously extract** the current **smallest/largest** element under changing data.
- **k-th largest** → size-k **min-heap**; **k-th smallest** → size-k **max-heap**.
- Heap’s top holds the **boundary** element you’re searching for; it filters the rest out.
- Think **Heap + X** = optimal extraction **plus** extra constraints.
- **Example — Task Scheduler**
  1) Extract the current highest-remaining task ⇒ **max-heap**.
  2) After running a task, it can’t run again for time `x` → hold it until available ⇒ **queue**.
- Other examples: **Heap + Sort** (interval scheduling) and **Heap + Pointer Walk** (k-th smallest in a matrix).

## Backtracking
- **DFS over the solution space** + **early pruning** of invalid branches (often “take/skip”).
- Add small helper structures for special rules: index, visited map, counts, etc.
- **Handling duplicates (take/skip):**
  - When you **skip**, advance to the **next distinct** number.
  - You already considered “take” on the first instance; taking later identical ones at the same depth creates **duplicate paths**.
  - Skipping the first instance ≈ taking a later identical instance → **collapse to one** by always advancing.

## Tries
- Implement as **nested hash maps**; a special key (e.g., `'*'`) marks **end-of-word**.
- Typical usage: run DFS and traverse each key’s hashmap unless it’s the end-marker.

## Graphs

### Bipartition
- Split nodes into **two groups** so every edge connects nodes in **opposite groups**.
- **Color** nodes as you traverse; colors **alternate** from a node to neighbors.
- Graphs can be **disconnected** → start a traversal from **every uncolored** node.

### Union–Find (Disjoint Set Union)
- Components build as edges connect nodes; each has a **representative**.
- Optimizations: **path compression** and **union by size/rank**.
- **Template sketch**
  - `parent = [i for i in range(n)]`
  - `size = [1]*n`
  - `find(x)`: while `x != parent[x]`: set `parent[x] = parent[parent[x]]`; move up; return root.
  - `union(a, b)`: find reps; if same, stop; attach **smaller** to **larger**; update size.

### Dijkstra’s
- **Weighted BFS**: min-heap of `(distance, node)`.
- Pop from heap; if node already finalized, **skip**.
- When relaxing a neighbor, push **current path + edge weight**.
- `dist` map/array stores best-known distances; `None`/∞ means **undiscovered**.

## Intervals
- **Sort intervals**, then sweep once to merge/pick/compare based on start/end.
- To merge overlapping: new start = **min(starts)**, new end = **max(ends)**.
- If `next.start > current.end` ⇒ **no overlap** (flush current, start new).

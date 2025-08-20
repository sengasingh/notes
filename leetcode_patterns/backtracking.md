# Backtracking Pattern

**Backtracking** is a **depth-first search** that builds a partial solution step by step and **backs up** as soon as a **constraint** is violated or the branch can’t beat the current best. Use it to **enumerate**, **decide (exists?)**, **count**, or **optimize** under constraints.

---

## 1) Why & When

- “**List all / count** valid configurations/strings/paths/subsets.”  
- “Does **any** arrangement/path exist that satisfies these **rules**?”  
- “Pick some items with **constraints** (sum/size/compatibility).”  
- **Constraint satisfaction**: N-Queens, Sudoku, graph coloring, word search.  
- **Orderings/arrangements**: permutations with/without duplicates.  
- **Branch & Bound optimization**: prune if we can’t beat the best so far.

**Key idea:** model the search as **(state, choices, constraints, goal)** and cut branches **early**.

---

## 2) Core Mental Model

- **DFS skeleton:** `choose → explore → unchoose`. Keep the **path** (partial solution).  
- **Prune early:** if partial state already breaks a rule or can’t meet a bound, **return now**.  
- **Ordering matters:** pick the **next decision** that **reduces branching** (e.g., cell with fewest options).  
- **Two stopping modes:**  
  - **Existence/first solution:** stop as soon as found.  
  - **Enumeration/optimization:** explore all (with dedup + pruning).  
- **State footprint ≈ recursion depth** → memory is usually **O(depth)**.

---

## 3) How to Recognize It

Phrases like:
- “**Generate all** … with rules / no repeats / must include X”  
- “Is there **a way** to arrange/place/assign … so that …?”  
- “**Subsets/Combinations/Permutations** under constraints”  
- “Place **N** items with **conflicts** / **no two attack** / **not adjacent**”  
- “Find a **path** in a grid using each cell once / spelling a word”  
- “Try options; **backtrack** on failure” (often implicit)

Classic cues: *N-Queens, Sudoku, Word Search, Palindrome Partitioning, Combination Sum, Subset Sum, Graph Coloring, Restore IP Addresses.*

---

## 4) Key Variants (Patterns)

- **Subsets / Combinations (no reuse):** advance an **index**; each item is **take or skip** (or choose next start).  
- **Permutations (use each once):** track a **used[]/bitmask**; place one position at a time.  
- **Handling duplicates:** **sort**; then **skip equal siblings** (`if i>start and a[i]==a[i-1]: continue`) or, for permutations, **skip if a[i]==a[i-1] and not used[i-1]**.  
- **Grid/backtracking with visited:** move 4/8 directions; mark/unmark **visited** per path.  
- **Constraint Satisfaction (CSP):** variables → domains; use **MRV** (pick var with fewest values), **forward checking** (prune neighbors).  
- **Branch & Bound:** keep **best_so_far**; compute **upper/lower bounds** to cut hopeless branches.  
- **Bitmask acceleration:** represent sets/columns/diagonals as **integers** for O(1) checks (e.g., N-Queens).  
- **Iterative deepening / depth limits:** when searching for **shortest solution** in an implicit tree.

---

## 5) Ultra-Compact Templates (Pseudo)

Generic skeleton
    
    def backtrack(state):
        if goal(state):
            record/return
            return
        for choice in valid_choices(state):
            apply(choice, state)       # choose
            backtrack(state)          # explore
            undo(choice, state)       # unchoose

Subsets (power set) / combinations by index
    
    res, path = [], []
    def dfs(i):
        if i == n:
            res.append(path.copy()); return
        # skip
        dfs(i+1)
        # take
        path.append(a[i])
        dfs(i+1)
        path.pop()
    dfs(0)

Combinations (choose k from n, no duplicates)
    
    res, path = [], []
    def dfs(start, k_left):
        if k_left == 0:
            res.append(path.copy()); return
        # pruning: not enough items left
        for i in range(start, n - k_left + 1):
            path.append(a[i])
            dfs(i+1, k_left-1)
            path.pop()
    dfs(0, k)

Permutations (distinct)
    
    res, path, used = [], [], [False]*n
    def dfs():
        if len(path) == n:
            res.append(path.copy()); return
        for i in range(n):
            if used[i]: continue
            used[i] = True; path.append(a[i])
            dfs()
            path.pop(); used[i] = False
    dfs()

Permutations with duplicates (sorted `a`)
    
    res, path, used = [], [], [False]*n
    def dfs():
        if len(path) == n:
            res.append(path.copy()); return
        for i in range(n):
            if used[i]: continue
            if i>0 and a[i]==a[i-1] and not used[i-1]: continue  # dedup
            used[i] = True; path.append(a[i])
            dfs()
            path.pop(); used[i] = False
    dfs()

Combination Sum (reuse allowed, nondecreasing; sorted `a`)
    
    res, path = [], []
    def dfs(start, remain):
        if remain == 0:
            res.append(path.copy()); return
        for i in range(start, n):
            if a[i] > remain: break   # prune
            path.append(a[i])
            dfs(i, remain - a[i])     # i (reuse), not i+1
            path.pop()
    dfs(0, target)

N-Queens (bitmasks: columns, diagonals)
    
    res, cols, d1, d2 = 0, 0, 0, 0     # d1: r-c, d2: r+c normalized in masks
    def dfs(r, cols, d1, d2):
        if r == n:
            nonlocal res; res += 1; return
        free = ((1<<n)-1) & ~(cols | d1 | d2)
        while free:
            p = free & -free          # pick lowest 1 bit
            free -= p
            dfs(r+1, cols|p, (d1|p)<<1 & ((1<<n)-1), (d2|p)>>1)
    dfs(0, cols, d1, d2)

Word Search (grid DFS with per-path visited)
    
    def exists(board, word):
        m, n = len(board), len(board[0])
        def dfs(r, c, k):
            if k == len(word): return True
            if r<0 or r>=m or c<0 or c>=n: return False
            if board[r][c] != word[k]: return False
            tmp, board[r][c] = board[r][c], '#'   # mark
            ok = dfs(r+1,c,k+1) or dfs(r-1,c,k+1) or dfs(r,c+1,k+1) or dfs(r,c-1,k+1)
            board[r][c] = tmp                     # unmark
            return ok
        for r in range(m):
            for c in range(n):
                if dfs(r,c,0): return True
        return False

Branch & Bound (maximize score with remaining bound)
    
    best, path = -INF, []
    def dfs(i, score, remain_bound):
        nonlocal best
        if score + remain_bound <= best: return    # prune
        if i == n:
            best = max(best, score); return
        # try including item i if valid
        if valid_include(i): dfs(i+1, score + val[i], remain_bound - bound_use(i))
        # skip i
        dfs(i+1, score, remain_bound - bound_skip(i))

---

## 6) Tips

- **Prune ASAP:** sort inputs to enable **early breaks** (e.g., sums), compute **tight bounds**, and **forward-check** constraints.  
- **Dedup correctly:**  
  - **Combinations:** `if i>start and a[i]==a[i-1]: continue`  
  - **Permutations:** `if i>0 and a[i]==a[i-1] and not used[i-1]: continue`  
- **Avoid needless copying:** `res.append(path.copy())`; use **append/pop** for O(1) updates.  
- **Use booleans/bitmasks** instead of `x in path` (O(1) vs O(n)).  
- **Separate global vs per-level visited:** grids often need **per-path** marking/unmarking.  
- **Pick hard decisions first** (MRV) to shrink the tree.  
- **Stop early** for existence problems; **short-circuit** on success.  
- **Watch recursion limits** if depth can be large; convert to an explicit stack when needed.

---

## 7) Pitfalls

- **Forgetting to undo** (not restoring state) → leaks across branches.  
- **Late constraint checks** → exponential blowup; validate **before** recursing.  
- **Wrong duplicate handling** → missing answers or duplicates.  
- **Global visited misuse** in grids/graphs → blocks valid paths.  
- **Ineffective bounds** in branch & bound → no real pruning.  
- **Using list membership as visited** → O(n) per check, slow.

---

## 8) Complexity Snapshot

| Problem / Pattern                      | Time (worst typical)           | Space (extra) |
|----------------------------------------|--------------------------------|---------------|
| Subsets of n                           | O(2^n · n)                     | O(n)          |
| Combinations C(n, k)                   | O(C(n,k) · k)                  | O(k)          |
| Permutations of n                      | O(n! · n)                      | O(n)          |
| Combination Sum (pruned)               | Exponential; ≈ O(b^d)          | O(d)          |
| N-Queens                               | Exponential; ≈ O(N!) (pruned)  | O(N)          |
| Word Search (m×n, word L)              | O(m·n·4^L)                     | O(L)          |
| CSP (Sudoku, coloring)                 | Exponential; pruning heavy     | O(depth)      |

**Rule of thumb:** search tree size ~ **O(b^d)** (branching `b`, depth `d`). Good **ordering + pruning** can turn impossible into instant.

---

*End of Notes*
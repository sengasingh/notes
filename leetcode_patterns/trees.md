# Trees Pattern

**Trees** model hierarchical structure. Most problems are about **traversal** (DFS/BFS), **combining child results** (postorder DP), or **carrying state down** (preorder). Think in **subtrees**: each node is a subproblem whose answer depends on its children.

---

## 1) Why & When

- **Hierarchy / containment:** folders, org charts, DOM, expression trees.
- **Divide & conquer on subtrees:** height, diameter, sums, counts, DP.
- **Ordered properties (BST):** search, validate, k-th, predecessor/successor.
- **Layered questions:** levels, shortest steps, zigzag → BFS.

**Key idea:** define a small contract per node (what the function **returns** and/or **updates**) and ensure it holds for the children before combining.

---

## 2) Core Mental Model

- **Traversal orders:**
  - **Preorder (node → children):** carry state downward (path prefix, running sums).
  - **Inorder (L → node → R):** BST gives **sorted** order; use bounds.
  - **Postorder (children → node):** aggregate **upwards** (heights, best-of-two, DP).
- **DFS vs BFS:**
  - **DFS (recursion/stack):** subtree computations, path-based tasks.
  - **BFS (queue):** level-by-level, shortest level distance, per-level views.
- **State handling:** either **return** values up (pure) or **update** an external accumulator for “best” answers.

---

## 3) How to Recognize It

- “Compute **height/diameter/sum**,” “how many **paths** …,” “**balanced** tree?”
- “**Level** order,” “**right/left view**,” “**shortest** steps between nodes.”
- “In a **BST**: k-th smallest, validate, predecessor/successor, range sum.”
- “**LCA** of two nodes,” “distance between nodes,” “build tree from traversals.”

These map to: **postorder DP**, **BFS by levels**, **BST invariants**, or **ancestor logic**.

---

## 4) Go-To Templates (Pseudo)

Postorder (aggregate up):
    
    function dfs(node):
        if node is null: return base
        L = dfs(node.left)
        R = dfs(node.right)
        return combine(node, L, R)

Preorder (carry down):
    
    function dfs(node, carry):
        if node is null: return
        carry2 = update(carry, node)
        maybe_record_answer(node, carry2)
        dfs(node.left, carry2)
        dfs(node.right, carry2)

BFS (levels):
    
    queue = [root] if root else []
    while queue:
        for _ in 1..len(queue):       # one level
            node = pop_front(queue)
            process(node)             # collect per-level info
            push children if exist

BST invariants:
    
    - Validation: each node.val within (low, high) from ancestors
    - Search/k-th: inorder gives sorted stream; count while visiting
    - Pred/succ: move left/right, remember last turn

---

## 5) LCA (lowest common ancestor) Intuition

- **General tree:** postorder—if a node’s left/right subtrees report the two targets (or node is one target and the other is below), that node is LCA.  
- **BST shortcut:** walk down; if `p` and `q` split at `node` (one ≤, one ≥), that `node` is LCA.

---

## 6) Pitfalls

- **Null/base cases:** return the right neutral element (0, −∞, None).
- **Return vs global:** choose one style and be consistent.
- **BST bounds:** use strict vs non-strict correctly (`low < val < high`).
- **Recursion depth on skewed trees:** consider iterative DFS.
- **Backtracking state:** undo path pushes when returning from recursion.

---

## 7) Complexity Snapshot

| Task                            | Time | Space              |
|---------------------------------|------|--------------------|
| DFS/BFS traversal               | O(n) | O(h) DFS, O(w) BFS |
| BST search / insert             | O(h) | O(1) iterative     |
| Tree DP (single-pass postorder) | O(n) | O(h)               |

*(n = nodes, h = height, w = max width / queue size)*

---

*End of Notes*
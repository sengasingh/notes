# Stack Pattern

The **stack** pattern uses **Last-In-First-Out (LIFO)** to *delay decisions* until you have enough info. You **push** items as you scan and **pop** while a chosen **invariant** is broken. This cleanly handles matching pairs, “nearest neighbor” queries, parsing nested structures, and simulating recursion—often in **O(n)**.

---

## 1) Why & When

- **Matching/Nesting:** parentheses, tags, string decoding, calculators.
- **Nearest Neighbor:** “next/previous greater/smaller,” stock span, daily temps.
- **Order/State Frames:** postfix evaluation; iterative DFS instead of recursion.
- **Greedy Pruning:** remove elements to keep the “best” subsequence.

**Key idea:** keep a property (the **invariant**) true at the top. While it’s violated, **pop** and finalize work (fill an answer, compute an area, prune).

---

## 2) Core Mental Model

- **Push when unsure; pop when sure.**  
- Define the **invariant** (e.g., “stack is decreasing by value”).  
- Prefer **indices** (not just values) when you’ll need distances/widths.  
- Use a **while**, not an if—one element can invalidate many.  
- Add a **sentinel** (e.g., a 0 at the end) to flush leftover work.

---

## 3) How to Recognize It

Phrases like:
- “**Next/previous** greater/smaller element”
- “**First** to the **left/right** that …”
- “How many days until a **warmer** day?”
- “**Span** / **width** while scanning”
- “**Balanced/valid** parentheses,” “nested structure,” “evaluate expression”
- “Remove elements to get **smallest/most competitive** subsequence”

These almost always map to a (often **monotonic**) stack.

---

## 4) Monotonic Stacks (the workhorse)

- Pick direction + monotonicity:  
  – **Next greater to the right:** keep a **decreasing** stack.  
  – **Next smaller:** keep an **increasing** stack.  
- **Equality rule** matters (`<` vs `<=`)—affects duplicates and popping.
- **Left vs right neighbors:** scan direction + what you pop decides which side you answer.
- **Circular arrays:** scan twice with `i % n`, but **write answers only once**.

---

## 5) Other Classic Uses

- **Delimiter matching:** push opens, pop on matching close; fail on mismatch/leftovers.  
- **Parsing/evaluation:** stacks for numbers/operators or sign/context through parentheses.  
- **Decoding strings:** push `(current_string, repeat)` at `[`, pop at `]` and expand.  
- **Iterative DFS/traversals:** simulate the call stack; push nodes as you go.

---

## 6) Ultra-Compact Template

    init empty stack S
    for each element x (often with index i):
        while S not empty and invariant_violated(S.top, x):
            resolve_with(S.pop())   # fill answer / compute area / prune
        S.push(x or (x, i))
    drain S if needed (resolve remaining)

---

## 7) Pitfalls

- Using **if** instead of **while** (misses cascaded pops)  
- Not storing **indices** when distances/widths are needed  
- Wrong **equality** handling for duplicates  
- Forgetting a **sentinel** to flush pending items  
- Delimiter mismatches: empty on close, or leftover opens at end

---

## 8) Complexity Snapshot

| Pattern                          | Time | Space |
|----------------------------------|------|-------|
| Monotonic scans / matching       | O(n) | O(n)  |
| Parsing/evaluation (single pass) | O(n) | O(n)  |
| Iterative DFS                    | O(n) | O(h)  |

---

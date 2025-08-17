# Linked List Pattern

**Linked lists** are pointer-based sequences where each node holds a value and a reference to the next node. They excel at **in-place reordering** and **local edits** (insert/delete) without shifting elements. Most problems boil down to **pointer surgery** (rewiring `next` links) and **two-pointer scans**—often in **O(n)** time and **O(1)** extra space.

---

## 1) Why & When

- **Frequent inserts/deletes** in the middle (no shifting like arrays).
- **Streaming/unknown length** sequences (process as you traverse).
- **In-place transforms:** reverse, reorder, partition, remove nodes.
- **Topology questions:** find **middle**, **cycle**, **intersection**, **nth from end**.

**Key idea:** Think in **pointers**, not indices. Keep a tiny set of moving references (`prev`, `curr`, `next`, sometimes `tail`), and rewire links carefully.

---

## 2) Core Mental Model

- Use a **dummy/sentinel head** to simplify edge cases (head removal, empty list).
- Maintain a small, stable set of **invariants** (e.g., “left side processed, right side unprocessed”).
- Move in **single pass** when possible; avoid counting unless needed.
- For any destructive step, first **save `next`**, then rewire, then advance.

---

## 3) How to Recognize It

Look for phrases like:
- “**Reverse** the list / sublist,” “**reorder** nodes,” “**partition** around x.”
- “Find **middle**,” “remove **nth from end**,” “**palindrome** list.”
- “Detect **cycle** / find **cycle start**.”
- “**Merge** two sorted lists,” “find **intersection** of two lists.”
- “Copy list with **random** pointer.”

These almost always use **two pointers** (sometimes fast/slow) plus minimal rewiring.

---

## 4) Key Techniques (What to Reach For)

- **Two Pointers (fast/slow):**  
  – **Middle:** advance `fast` 2x, `slow` 1x → `slow` lands at middle.  
  – **Cycle detect:** if `fast` meets `slow`, there’s a cycle; to find start, reset one pointer to head and step both 1x.  
  – **Nth from end:** move `fast` `k` steps, then advance both until `fast` hits null; `slow` points to node before target.

- **Reverse In-Place (workhorse):**  
  Maintain `prev = None`, `curr = head`. For each step:  
  `next = curr.next` → `curr.next = prev` → `prev = curr` → `curr = next`.  
  Use it for full reverse, **second-half reverse** (palindrome), **k-group** blocks, etc.

- **Dummy Head + Splicing:**  
  Keep `dummy -> head`, track `prev` (last node in the “built” side) and `curr` (node to process). To **delete**, do `prev.next = curr.next`. To **insert**, adjust links around `prev`.

- **Length Alignment (intersection):**  
  Compute lengths `L1, L2`; advance the longer list by `|L1-L2|`, then step together to meet.

- **Two-List Merge (sorted):**  
  Carry a `tail` pointer; repeatedly take the smaller head from `A`/`B`, splice after `tail`, advance.

- **Copy with Random Pointer:**  
  Either a hashmap (`orig → clone`) in O(n) space, or **interleave clone nodes** between originals, fix randoms, then split.

---

## 5) Ultra-Compact Templates (Pseudo)

**A) Two-Pointers (nth from end)**  
- Move `fast` `k` times.  
- While `fast`: advance `fast` & `slow`.  
- Now `slow` is just before the target; rewire/delete as needed.

**B) Reverse (in-place)**  
- Loop: save `next`, point `curr.next` to `prev`, advance `prev` and `curr`.  
- Result head is `prev`.

**C) Palindrome Check**  
- Find middle (fast/slow) → reverse second half → compare first vs reversed second → (optional) restore.

---

## 6) Pitfalls

- **Losing the rest:** don’t overwrite `next` before saving it.  
- **Head edge cases:** removing the head; use a **dummy**.  
- **Off-by-one:** `k` vs `k-1`, especially for “nth from end.”  
- **Infinite loops:** cycles or forgetting to null-terminate the tail.  
- **Cycle-unsafe traversals:** always consider whether input might be cyclic.

---

## 7) Complexity Snapshot

| Task                                | Time | Space |
|-------------------------------------|------|-------|
| Reverse / reorder / delete / merge  | O(n) | O(1)  |
| Cycle detection (Floyd)             | O(n) | O(1)  |
| Copy with random (hash / interleave)| O(n) | O(n) / O(1) |

---

*End of Notes*

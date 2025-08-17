# Binary Search Pattern

**Binary search** narrows an answer by repeatedly halving a **monotonic** search space. It’s ideal when the domain (indices, numbers, or “answers”) has a **boundary** where a predicate flips from **false → true** (or vice versa). Typical wins are **O(log n)** time with **O(1)** space.

---

## 1) Why & When

- **Sorted data:** find a value, first/last occurrence, insertion point.
- **Boundary queries:** “first `x` such that condition holds,” “last `x` before it fails.”
- **Parametric search (aka “binary search on answer”):** minimize/maximize a quantity subject to a check (speed, capacity, distance).
- **Unimodal/peaks:** mountain arrays, local maxima (under suitable structure).
- **Real-valued searches:** continuous domains where a monotone check exists.

**Key idea:** Don’t search for the answer directly—search for the **boundary** where a simple **predicate** changes.

---

## 2) Core Mental Model

- Define a **predicate** `good(x)` that is **monotonic** (if `x` is good, then all larger/smaller `x` are also good).
- Maintain an **invariant** over a range: e.g., “`lo` is **bad**, `hi` is **good**.”
- Repeatedly pick `mid` and move the boundary to keep the invariant true.
- Stop when the range collapses—what remains is the boundary (your answer).

---

## 3) How to Recognize It

Phrases like:
- “**First/last** time/index/value where …”
- “**Minimum/maximum** `x` such that …”
- “Can we do it with **at most** K … ?” → yes/no feasibility check
- “**Insert position** in sorted order,” “**count** of elements ≤ `x`”
- “**Capacity/speed** to finish in D days,” “**maximize minimum** distance”

---

## 4) Key Variants

- **On indices (classic):** find target, lower_bound (first ≥ t), upper_bound (first > t), first/last occurrence.
- **On rotated arrays:** decide which half is sorted; narrow accordingly.
- **On 2D sorted structures:** map to 1D index or row/col elimination.
- **On answer space (parametric):** choose `lo/hi` as plausible answer bounds; `good(mid)` = “feasible?”.
- **On reals:** iterate fixed times or until `hi - lo < eps`.

---

## 5) Ultra-Compact Templates

**A) First True (Boundary)**
    
    # invariant: lo is bad, hi is good
    while lo < hi:
        mid = (lo + hi) // 2
        if good(mid):
            hi = mid
        else:
            lo = mid + 1
    return lo  # first good

**B) Last True (Mirror)**

    # invariant: lo is good, hi is bad
    while lo < hi:
        mid = (lo + hi + 1) // 2   # bias right to avoid infinite loop
        if good(mid):
            lo = mid
        else:
            hi = mid - 1
    return lo  # last good

**C) Binary Search on Answer (Feasibility)**

    # pick numeric bounds [lo, hi]; define good(x) as "feasible?"
    apply template A or B to minimize/maximize the answer

---

## 6) Tuning & Tips

- **Pick the right invariant** up front (who is “good/bad”?).
- **Bias mid** (`(lo+hi+1)//2`) when shrinking the left side to avoid stalling.
- **Duplicates:** use “first/last true” framing to get exact boundaries.
- **Bounds matter:** choose tight but safe initial `lo/hi`.
- **Think in decisions, not values:** write `good(x)` first; code later.

---

## 7) Pitfalls

- **Non-monotone predicate:** binary search won’t work—reformulate.
- **Off-by-one / infinite loop:** wrong mid bias or incorrect boundary update.
- **Overflow:** use `mid = lo + (hi - lo) // 2`.
- **Wrong equality rule:** mixing `<` vs `<=` flips which side you keep.
- **Rotated arrays:** forgetting to check which half is sorted before pruning.

---

## 8) Complexity Snapshot

| Scenario                          | Time                 | Space |
|-----------------------------------|----------------------|-------|
| On sorted array / index domain    | O(log n)             | O(1)  |
| On numeric answer (feasibility)   | O(log range * check) | O(1)  |
| On reals (precision `eps`)        | O(log((hi−lo)/eps))  | O(1)  |

---

*End of Notes*

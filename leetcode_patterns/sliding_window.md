# Sliding Window Pattern

The **sliding window** pattern uses two pointers (`left` and `right`) to define a contiguous subarray or substring (“window”) and adjusts its size or position dynamically to satisfy a condition. By ensuring each element is added and removed at most once, it runs in **O(n)** time, avoiding nested loops.

---

## 1. Why & When to Use Sliding Window

- **Contiguous Subarray/Substring**: ideal for problems requiring examination of contiguous elements (e.g., sums, counts, unique properties).  
- **Dynamic Constraints**: when a condition (sum ≥ target, at most k distinct, fixed window size) dictates window growth or shrinkage.  
- **Linear Time**: each element enters and exits the window only once.

**Key Insight:** maintain current window metrics (sum, counts, max/min) and move pointers based on whether the window meets or violates the target condition.

---

## 2. General Templates

### 2.1 Fixed-Size Window  
Use when the window length `k` is known in advance (e.g., maximum over every subarray of size `k`).

```pseudo
left = 0
window_metric = init
for right in 0..n-1:
    add arr[right] to window_metric
    if right - left + 1 == k:
        record result from window_metric
        remove arr[left] from window_metric
        left += 1
```

### 2.2 Variable-Size Window  
Use when the window expands until a constraint is met, then shrinks to try to regain it (e.g., sum ≥ target, at most k distinct characters).

```pseudo
left = 0
window_metric = init
for right in 0..n-1:
    add arr[right] to window_metric
    while window_metric violates constraint:
        remove arr[left] from window_metric
        left += 1
    # window now satisfies constraint → record/update result
```

---

## 3. Common Variants

| Variant                                    | Type           | LeetCode Examples                                  |
|--------------------------------------------|----------------|----------------------------------------------------|
| Sliding Window Maximum                     | Fixed-Size     | 239. Sliding Window Maximum                        |
| Sliding Window Median                      | Fixed-Size     | 480. Sliding Window Median (Hard)                  |
| Longest Substring Without Repeating Chars  | Variable-Size  | 3. Longest Substring Without Repeating Characters  |
| Minimum Size Subarray Sum                  | Variable-Size  | 209. Minimum Size Subarray Sum                     |
| Longest Repeating Character Replacement    | Variable-Size  | 424. Longest Repeating Character Replacement       |
| Minimum Window Substring                   | Variable-Size  | 76. Minimum Window Substring                       |
| Fruit Into Baskets (≤ 2 distinct)          | Variable-Size  | 904. Fruit Into Baskets                            |

---

## 4. Step-by-Step Reasoning

1. **Determine Window Type**  
   - **Fixed**: known length `k`.  
   - **Variable**: grows/shrinks based on a condition.

2. **Choose Data Structure**  
   - **Sum/Count**: simple integer accumulator.  
   - **Unique Tracking**: hash map or set for counts/indices.  
   - **Min/Max**: deque for monotonic queue.

3. **Initialize**  
   - `left = 0`, initialize metrics (sum = 0, map = {}, deque = empty).

4. **Expand Window**  
   - Move `right`, update metrics with `arr[right]`.

5. **Shrink Window (Variable Only)**  
   - While constraint violated, remove `arr[left]`, increment `left`.

6. **Update Result**  
   - **Fixed**: when window size == k.  
   - **Variable**: whenever window satisfies constraint (update min length, max length, etc.).

7. **Handle Edge Cases**  
   - Empty input → return default.  
   - No valid window → return 0 or empty.  
   - k > n (fixed) → handle as no valid window.

---

## 5. Example Walkthrough: Longest Substring Without Repeating Characters (LeetCode #3)

**Problem**: Find the length of the longest substring without repeating characters in a string `s`.

1. **Window Type**: variable-size (all chars must be unique).  
2. **Data Structure**: hash map `last_index` mapping char → most recent index.  
3. **Algorithm**:
```pseudo
left = 0
max_len = 0
last_index = {}
for right in 0..len(s)-1:
    if s[right] in last_index and last_index[s[right]] >= left:
        left = last_index[s[right]] + 1
    last_index[s[right]] = right
    max_len = max(max_len, right - left + 1)
return max_len
```
4. **Walkthrough**:  
   - As `right` moves, if we see a duplicate inside the current window, slide `left` just past its previous occurrence.  
   - Update `max_len` after each step.

```python
def length_of_longest_substring(s):
    last_index = {}
    left = max_len = 0
    for right, ch in enumerate(s):
        if ch in last_index and last_index[ch] >= left:
            left = last_index[ch] + 1
        last_index[ch] = right
        max_len = max(max_len, right - left + 1)
    return max_len
```

- **Time**: O(n)  
- **Space**: O(min(n, m)), where m = size of the character set

---

## 6. Tips for Mastery

- **Visualize the Window**: draw indices on paper for sample inputs.  
- **Modularize**: write helper functions `add(index)`, `remove(index)` for window metrics.  
- **Debug Prints**: log `left`, `right`, and current metric during practice.  
- **Monotonic Queue**: use a deque for sliding min/max to keep O(1) updates.  
- **Avoid Backtracking**: ensure `left` only ever moves forward.

---

## 7. Complexity Summary

| Operation                           | Time Complexity | Space Complexity     |
|-------------------------------------|-----------------|----------------------|
| Fixed-Size Window (sum/count)      | O(n)            | O(1)                 |
| Fixed-Size Window (monotonic queue)| O(n)            | O(k)                 |
| Variable-Size Window (sum/count)    | O(n)            | O(1)                 |
| Variable-Size Window (unique count) | O(n)            | O(m)                 |

*m = number of distinct elements or character set*

---

*End of Notes*

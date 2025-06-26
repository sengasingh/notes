# Two Pointers Pattern

A **two pointers** approach uses two indices (usually called `left` and `right`) that move through an array (or string) from opposite ends—or sometimes in the same direction—to reduce the search space and achieve **O(n)** or **O(n log n)** time instead of **O(n²)**. It’s a go-to pattern whenever you need to find pairs, triplets, or subarrays that satisfy some condition.

---

## 1. Why & When to Use Two Pointers

- **Sorted Input** (or you can sort in O(n log n)): you can advance pointers based on comparison.  
- **Pair/Sum Problems**: find two elements that meet a target (e.g., sum, difference).  
- **Container/Window Problems**: maximize/minimize something between two indices.  
- **In-Place Rearrangement**: partitioning around a pivot (e.g., 0s & 1s, Dutch National Flag).  
- **Strings**: palindrome checks, reversing words, merging.

**Key Insight:** by moving `left` and `right` based on the current state, you eliminate large swaths of impossible solutions in one step.

---

## 2. General Template

```pseudo
function twoPointers(arr):
    sort(arr)            # if not already sorted
    left  ← 0
    right ← len(arr) - 1

    while left < right:
        current = arr[left] + arr[right]    # or other relation
        if meetsTarget(current):
            handleSolution(left, right)
            # possibly move one or both pointers
        elif current < target:
            left += 1
        else:
            right -= 1
```

- **Initialization**: `left` at start, `right` at end.  
- **Loop Condition**: usually `while left < right`.  
- **Move Logic**: based on comparison to the goal, advance one pointer to get closer.  
- **Solution Handling**: when you find a valid pair/triple/window, record it and decide how to continue (stop? skip duplicates? shrink window?).

---

## 3. Common Variants

| Variant                       | Description                                              | LeetCode Examples                                      |
|-------------------------------|----------------------------------------------------------|---------------------------------------------------------|
| 3-Sum / 4-Sum                 | Fix one pointer, use two pointers on remainder           | 15. 3Sum, 18. 4Sum                                       |
| Closest Sum                   | Find pair/triple whose sum is closest to target          | 16. 3Sum Closest                                        |
| Container With Most Water     | Maximize area between two lines                          | 11. Container With Most Water                           |
| Subarray Problems             | Find subarray with given property; shrink/expand window  | 209. Minimum Size Subarray Sum                         |
| Partitioning                  | Move smaller/larger elements to either side in-place     | 75. Sort Colors                                         |
| Palindrome / Reverse String   | Check or build by comparing/moving from both ends        | 125. Valid Palindrome, 344. Reverse String              |

---

## 4. Step-by-Step Reasoning

1. **Understand the Goal**  
   – Are you finding a pair/triple? Maximizing/minimizing an area or sum?  
   – Does the input need sorting?  

2. **Decide Pointer Roles**  
   – `left` = start, `right` = end for sum-oriented or area-oriented problems.  
   – Or both pointers march forward (e.g., current window boundaries).

3. **Invariants & Movement**  
   – When you increase `left`, you eliminate all pairs with that `left` index and any `right′ < right`.  
   – When you decrease `right`, you eliminate all pairs with that `right` index and any `left′ > left`.

4. **Handling Solutions**  
   – **One solution**: return immediately.  
   – **All solutions**: record and then move pointers past duplicates:  
     ```pseudo
     while left < right and arr[left] == arr[left+1]:
         left += 1
     while left < right and arr[right] == arr[right-1]:
         right -= 1
     ```  
   – **Count only**: just increment a counter and move pointers.

5. **Edge Cases**  
   – Empty array, single element → typically return zero or empty set.  
   – All same values → skip duplicates carefully.  
   – Negative numbers → sorting step handles sign.

---

## 5. Example Walkthrough: 3Sum (LeetCode 15)

**Problem**: Find all unique triplets `[i, j, k]` so that `nums[i] + nums[j] + nums[k] == 0`.

1. **Sort** `nums` → O(n log n).  
2. **Fix** an index `i` from `0` to `n − 3`.  
   - Skip duplicates: `if i > 0 and nums[i] == nums[i-1]: continue`.  
3. **Two pointers** on subarray `i+1 … n − 1`:  
   ```pseudo
   left  ← i + 1
   right ← n − 1
   while left < right:
       sum3 = nums[i] + nums[left] + nums[right]
       if sum3 == 0:
           add [nums[i], nums[left], nums[right]] to result
           left += 1
           right -= 1
           skip duplicates on both sides
       elif sum3 < 0:
           left += 1
       else:
           right -= 1
   ```
4. **Collect** all unique triplets.

- **Time**: O(n²).  
- **Space**: O(1) extra (plus output).

---

## 6. Tips for Mastery

- **Always sort** when the relationship between elements (sum, difference, product) matters.  
- **Draw it out**: mark `left`/`right` on a number line of the array.  
- **Practice edge cases**: all positive, all negative, many duplicates.  
- **Template drill**: write the two-pointer template from memory daily until fluid.  
- **Combine patterns**: e.g., two pointers inside recursion for k-Sum, or sliding window for variable-size windows.

---

## Complexity Summary

| Operation             | Time Complexity  | Space Complexity            |
|-----------------------|------------------|-----------------------------|
| Sorting (if needed)   | O(n log n)       | O(log n)¹                   |
| Two-pointer scan      | O(n)             | O(1)                        |
| Overall (sum problems)| O(n²) for 3Sum   | O(1) (plus output storage)  |

¹ Depends on language/platform recursion depth for sort.

test yo

---

*End of Notes*
# Arrays & Hashing Pattern

The **arrays & hashing** pattern leverages hash-based data structures (typically **hash maps** and **hash sets**) to trade extra space for faster lookup, insertion, and frequency counting, achieving **O(n)** or **O(n log n)** time in many scenarios instead of brute-force **O(n²)**. It’s ideal whenever you need to track occurrences, detect complements, group items by key, or enforce uniqueness.

---

## 1. Why & When to Use Arrays & Hashing

- **Fast Lookups:** checking existence or fetching a stored value in average **O(1)**.  
- **Counting Frequencies:** tallying elements (e.g., for majority, k-frequency problems).  
- **Complement Search:** find if a “partner” element has appeared (e.g., Two Sum).  
- **Grouping:** bucket elements by a derived key (e.g., anagram groups).  
- **Deduplication/Uniqueness:** detect duplicates or enforce one‐of‐each.  
- **Sliding Window with Counts:** maintain dynamic frequency counts over a window.  

**Key Insight:** by storing partial information (counts, indices, seen flags) in a hash structure as you traverse once (or twice), you avoid nested loops.

---

## 2. General Template

\`\`\`pseudo
function arraysAndHashing(arr):
    initialize hash_map or hash_set

    for each element (and possibly its index) in arr:
        if problem is “complement search”:
            complement = target - element
            if complement in hash_map:
                handleSolution(complement, element, hash_map)
        if problem is “counting”:
            hash_map[element] = hash_map.get(element, 0) + 1
        if problem is “grouping”:
            key = deriveKey(element)
            append element to hash_map[key]
        # other logic (window maintenance, uniqueness checks, etc.)

    if needed:
        post-process hash_map (e.g., extract keys, sort frequencies)
    return result
\`\`\`

- **Initialization:** choose a map (\`key → value\`) or set (\`values only\`).  
- **Single Pass:** build or query the hash as you go.  
- **Solution Handling:** whenever the condition meets, record or return.  
- **Post-processing:** for some problems (e.g., top k), you may need extra steps.

---

## 3. Common Variants

| Variant                          | Description                                             | LeetCode Examples                                      |
|----------------------------------|---------------------------------------------------------|---------------------------------------------------------|
| Two Sum                          | Find two indices summing to target                      | 1. Two Sum                                              |
| Contains Duplicate               | Detect any duplicates in an array                       | 217. Contains Duplicate                                 |
| Valid Anagram                    | Check if two strings are anagrams                       | 242. Valid Anagram                                      |
| Group Anagrams                   | Bucket strings by sorted‐char key                       | 49. Group Anagrams                                      |
| Top K Frequent Elements          | Find k elements with highest frequency                  | 347. Top K Frequent Elements                            |
| Longest Consecutive Sequence     | Track presence to build length of consecutive run       | 128. Longest Consecutive Sequence                       |
| Subarray Sum Equals K            | Count subarrays summing to k using prefix sums & map    | 560. Subarray Sum Equals K                              |
| Minimum Window Substring (hash)  | Sliding window with char counts                         | 76. Minimum Window Substring                            |

---

## 4. Step-by-Step Reasoning

1. **Identify Key Operation**  
   – Are you checking “have I seen X before?” → **hash set**.  
   – Do you need to map each value to a count, index, or list? → **hash map**.

2. **Decide Passes**  
   – **Single pass** for one-pass detection (e.g., Two Sum).  
   – **Two passes** for preprocessing frequencies (e.g., Valid Anagram).

3. **Build & Query Together**  
   – For complements: check map first, then insert current element.  
   – For counts: increment as you traverse.

4. **Handle Collisions & Edge Cases**  
   – Large inputs → watch out for integer overflow or memory limits.  
   – Empty array or null input → return default (e.g., empty list).  
   – k = 0 or k ≥ n → check bounds.

5. **Post-Processing**  
   – Sorting by frequency or key if required.  
   – Extracting only keys or values from the map.

---

## 5. Example Walkthrough: Two Sum (LeetCode #1)

**Problem**: Given \`nums\` and \`target\`, return indices of two numbers that add up to \`target\`. You may assume each input has exactly one solution, and you may not use the same element twice.

1. **Initialize** an empty map \`idx_by_val\`.  
2. **Iterate** over \`nums\` with \`for i, num in enumerate(nums)\`:  
   - Compute \`complement = target - num\`.  
   - **If** \`complement\` is already in \`idx_by_val\`:  
     - Return \`[ idx_by_val[complement], i ]\`.  
   - **Else**, store \`idx_by_val[num] = i\`.  
3. **Time**: O(n) single pass; **Space**: O(n) for the map.

\`\`\`python
def two_sum(nums, target):
    idx_by_val = {}
    for i, num in enumerate(nums):
        comp = target - num
        if comp in idx_by_val:
            return [idx_by_val[comp], i]
        idx_by_val[num] = i
\`\`\`

---

## 6. Tips for Mastery

- **Order of Operations**: when checking complements, always query *before* inserting the current element to avoid using the same element twice.  
- **Use \`.get()\` Wisely**: for counts, \`count = freq.get(x, 0) + 1\`.  
- **Key Derivation**: for grouping (e.g., anagrams), use immutables like sorted tuple of chars or character-count signature.  
- **Memory vs. Speed**: hashing trades memory for time, so ensure constraints allow O(n) extra space.  
- **Practice Edge Limits**: huge arrays, negative numbers, k = 0, empty strings.

---

## 7. Complexity Summary

| Operation                         | Time Complexity     | Space Complexity  |
|-----------------------------------|---------------------|-------------------|
| Single-pass build & query        | O(n)                | O(n)              |
| Two-pass count & compare         | O(n)                | O(n)              |
| Grouping + sort of keys/values   | O(n log n) overall  | O(n)              |
| Sliding window with counts       | O(n)                | O(k) (window size)|

---

*End of Notes*
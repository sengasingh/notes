# Tries / Prefix Trees Pattern (Nested HashMap Edition)

A **trie (prefix tree)** stores strings by characters in **nested hash maps** (`dict` of `dict`s). It makes **prefix queries** (“starts with…”) and exact lookups **O(L)** where `L` is the query length, and it supports autocomplete, counting by prefix, and simple wildcard search.

---

## 1) Why & When

- **Dictionary membership** (`search`) & **startsWith** checks  
- **Autocomplete / suggestions** from a prefix  
- **Count words** sharing a prefix; **shortest prefix match** (replace by roots)  
- **Wildcard search** (e.g., `.` matches any letter)  
- (Optional) **Bitwise trie** for XOR on integers (same idea; keys are 0/1)

**Key idea:** decisions are **O(1)** per character/bit; cost scales with **query length**, not with number of words.

---

## 2) Core Mental Model (Nested Dict)

- Each node is a **dict**: keys are **characters** (or **bits**), values are **child dicts**.  
- Reserve keys for metadata:
  - `END = "_end"` → count of words ending here (enables duplicates)  
  - `PASS = "_pass"` → count of words passing through (enables counting & safe deletes)
- **Insert**: walk/`setdefault` per char; bump `PASS`; bump `END` at the end.  
- **Search/startsWith**: walk chars; fail fast if a key is missing.  
- **Delete**: decrement `PASS` along the path; **prune** child dicts that become empty.

---

## 3) How to Recognize It

Phrases like: “**starts with**”, “**autocomplete**”, “**count with prefix**”, “**shortest/longest prefix match**”, “pattern with **wildcards**”, “**max XOR pair**”.

---

## 4) Key Variants (Patterns)

- **Plain trie (nested dict)** — simplest, works with any alphabet (incl. Unicode)  
- **Compressed (radix) edges** — optional space saver (collapse single-child chains)  
- **Bitwise trie (0/1 keys)** — for XOR problems on integers

---

## 5) Ultra-Compact Blueprints (Python-ish, Nested Dict)

> Uses reserved keys that won’t collide with real characters: `"_end"`, `"_pass"`.

    END, PASS = "_end", "_pass"

    def make_trie():
        return {PASS: 0}  # root node

    def insert(root, word):
        node = root
        node[PASS] = node.get(PASS, 0) + 1
        for ch in word:
            node = node.setdefault(ch, {PASS: 0})
            node[PASS] = node.get(PASS, 0) + 1
        node[END] = node.get(END, 0) + 1

    def search(root, word):          # exact word?
        node = root
        for ch in word:
            node = node.get(ch)
            if node is None:
                return False
        return node.get(END, 0) > 0

    def startsWith(root, pref):      # any word with this prefix?
        node = root
        for ch in pref:
            node = node.get(ch)
            if node is None:
                return False
        return True

    def countPrefix(root, pref):     # how many words share this prefix?
        node = root
        for ch in pref:
            node = node.get(ch)
            if node is None:
                return 0
        return node.get(PASS, 0)

    def delete(root, word):          # delete once if present; prune as needed
        if not search(root, word):
            return False
        stack, node = [], root
        node[PASS] -= 1
        for ch in word:
            nxt = node[ch]
            nxt[PASS] -= 1
            stack.append((node, ch, nxt))
            node = nxt
        node[END] -= 1
        # prune trailing empty nodes
        while stack:
            parent, ch, child = stack.pop()
            child_keys = [k for k in child if k not in (END, PASS)]
            if child.get(END, 0) == 0 and child.get(PASS, 0) == 0 and not child_keys:
                del parent[ch]
            else:
                break
        return True

**Blueprints (no full code):**
- **Autocomplete up to k:** walk to prefix node → DFS/BFS children; collect words until k (skip `END`/`PASS` keys).
- **Shortest prefix replacement:** from root, walk chars; return earliest point where `END>0`; else original word.
- **Wildcard search (`.`):** DFS over pattern; if char is `.`, try all child keys except `END`/`PASS`.

---

## 6) Tips

- Pick **reserved keys** outside your alphabet (e.g., `_end`, `_pass`) to avoid collisions.  
- Normalize inputs (case, accents) consistently.  
- `PASS` + `END` enable **counts** and **safe pruning**.  
- For ordering (autocomplete), sort child keys once per node when traversing.  
- If memory is tight, consider **compressing chains** (radix) later.

---

## 7) Pitfalls

- Forgetting to mark **end-of-word** → exact matches fail.  
- Using real characters as metadata keys → collisions.  
- Deleting without **ref counts** → either leaks or over-deletes.  
- Wildcard search iterating metadata keys → wrong results / wasted work.

---

## 8) Complexity Snapshot

| Operation / Pattern                   | Time (L = word/prefix length) | Space |
|--------------------------------------|--------------------------------|-------|
| Insert / Search / StartsWith         | **O(L)**                       | —     |
| Delete                               | **O(L)**                       | —     |
| Count words with prefix P            | **O(|P|)**                     | —     |
| Build trie for N words (T chars)     | **O(T)**                       | **O(T)** |

**Rule of thumb:** with a nested-dict trie, runtime depends on the **query length**, not the dictionary size.

---

*End of Notes*
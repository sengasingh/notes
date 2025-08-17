# Heaps / Priority Queues Pattern

A **heap / priority queue** keeps the **best** item (smallest/largest by a key) at the top with cheap **peek (O(1))** and **push/pop (O(log n))**. Use it whenever you must **repeatedly pick the next best** element, maintain the **top-k**, or drive a **greedy** / **event-simulation** process.

---

## 1) Why & When

- **Top-k / k-th** smallest/largest, running best/worst in a stream.  
- **Merge** many sorted inputs (k-way merge).  
- **Scheduling / intervals:** earliest finish/start (e.g., meeting rooms).  
- **Shortest paths / greedy graph DP:** Dijkstra, Prim.  
- **Order statistics over time:** running **median** (two heaps).  
- **Event sims:** always handle the next imminent event.

**Key idea:** store items as **(priority, payload)**; the heap surfaces the next decision cheaply.

---

## 2) Core Mental Model

- Choose **min-heap** for “smallest first,” **max-heap** for “largest first” (or negate keys if only min-heap exists).  
- Keep the heap **as small as possible** (often size `k`) to cut costs to **O(n log k)**.  
- For changing priorities (e.g., Dijkstra), **don’t update in place**—**push new** and **skip stale** when popped.  
- For interval scheduling, **sort by start**, track a **min-heap of end times**.

---

## 3) How to Recognize It

Phrases like:
- “**Top k** / **k-th** / **closest** / **most frequent**”
- “Repeatedly pick the **earliest**/**smallest**/**cheapest**”
- “**Merge k** sorted lists/files”
- “**Median** of a stream”
- “**Shortest path** with non-negative weights”
- “How many **rooms/machines** are needed at once?”

These are classic heap cues.

---

## 4) Key Variants (Patterns)

- **Bounded heap (size k):** keep the best k; pop when size exceeds k.  
- **K-way merge:** push first element of each list; pop → emit → push its successor.  
- **Meeting rooms:** push meeting end times; when a meeting starts, pop all ends ≤ start.  
- **Dijkstra/Prim:** push `(cost, node)`; on pop, **skip if stale** (cost > best[node]).  
- **Two-heaps median:** max-heap for lower half, min-heap for upper; rebalance so sizes differ ≤ 1.

---

## 5) Ultra-Compact Templates (Pseudo)

Top-k (largest): maintain a min-heap of size k
    
    heap = []  # keeps k largest seen
    for x in stream:
        push x into heap
        if len(heap) > k: pop smallest
    # heap holds the k largest

K-way merge
    
    heap = [(val0(i), i, idx=0) for each list i if not empty]
    while heap:
        val, i, j = pop()
        output val
        if j+1 < len(list_i): push (list_i[j+1], i, j+1)

Dijkstra (skip stale)
    
    best = {source: 0}
    heap = [(0, source)]
    while heap:
        d,u = pop()
        if d != best[u]: continue  # stale
        for (v,w) in adj[u]:
            if d + w < best.get(v, INF):
                best[v] = d + w
                push (best[v], v)

Two-heaps median
    
    max_low, min_high
    # insert x into one heap, then rebalance so |sizes| ≤ 1 and max_low.top ≤ min_high.top
    # median = top(max_low) or avg(top(max_low), top(min_high))

---

## 6) Tips

- Use **(key, tie_breaker, payload)** tuples to avoid comparator issues on ties.  
- For **max-heap** where only min-heap exists, store **(−key, payload)**.  
- Heaps don’t keep global order—only the **top** is ordered; pop repeatedly to get the next.  
- Combine with a **hash map** for counts/“lazy deletion” (ignore popped items whose count went to zero).

---

## 7) Pitfalls

- **Forgetting stale checks** (graph algorithms, sliding windows) → wrong answers.  
- **Using the wrong heap direction** (min vs max) for top-k logic.  
- **Letting the heap grow to n** when only **k** is needed.  
- **Assuming sorted iteration** from a heap—it only guarantees the current best, not full order.

---

## 8) Complexity Snapshot

| Operation / Pattern              | Time                | Space |
|---------------------------------|---------------------|-------|
| push / pop / peek               | O(log n) / O(log n) / O(1) | O(n)  |
| Build heap from n items         | O(n)                | O(n)  |
| Top-k over n items              | O(n log k)          | O(k)  |
| K-way merge (total N, k lists)  | O(N log k)          | O(k)  |
| Dijkstra (binary heap)          | O((E+V) log V)      | O(V)  |

---

*End of Notes*

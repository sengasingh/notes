# Graphs Pattern (incl. Dijkstra, Prim/Kruskal, Bellman–Ford)

Graphs model entities (**V**) and relationships (**E**). Use them for **reachability**, **shortest paths**, **ordering with prerequisites**, and **minimum-cost connection**.

---

## 1) Why & When

- **Reachability / components / cycles / bipartite**
- **Shortest paths:** unweighted (**BFS**), non-negative (**Dijkstra**), with negatives (**Bellman–Ford**), small all-pairs (**Floyd–Warshall**)
- **Ordering on DAGs:** topological sort + DP
- **Minimum Spanning Tree (MST):** **Prim** / **Kruskal**

**Key idea:** match the **weight rules** and **structure** to the right algorithm.

---

## 2) Core Mental Model

- Represent with **adjacency lists**: `adj[u] = [(v, w)]` (or just `v` if unweighted).
- **BFS** explores in **layers** (edge cost = 1).
- **DFS** dives deep; good for **components**, **cycles**, **toposort** (via finish order).
- **Relaxation** drives shortest paths: try to improve `dist[v]` via `u`.
- **Heaps** surface the **next best** frontier (Dijkstra, Prim).
- **DSU / Union–Find** tracks components efficiently (Kruskal).

---

## 3) How to Recognize It

Phrases like:
- “**Fewest steps / minimum cost / shortest route**”
- “**Can A reach B? How many groups?**”
- “**Order tasks** with prerequisites”
- “**Connect all nodes** at **minimum total cost**”

---

## 4) Algorithm Catalog (When to Use What)

- **BFS (unweighted shortest path):** all edges cost 1 → **O(V+E)**.
- **0–1 BFS:** weights in {0,1}; use a **deque** (0 → push front, 1 → push back).
- **Dijkstra:** **non-negative** weights; min-heap; **skip stale** pops.
- **Bellman–Ford:** handles **negative edges** and **detects negative cycles**; slower.
- **DAG shortest paths:** topo order, then one pass of relaxations (linear).
- **Prim (MST):** grow tree via cheapest crossing edge (min-heap).
- **Kruskal (MST):** sort edges; add if endpoints are in different components (DSU).
- **Floyd–Warshall:** small graphs; **all pairs** shortest paths.

---

## 5) Ultra-Compact Blueprints (Pseudo — one-screen, copy-clean)

**BFS (unweighted shortest)**
    
    dist[src]=0; parent={src:None}; q=[src]
    while q:
        u = pop_left(q)
        for v in adj[u]:
            if v not in dist:
                dist[v]=dist[u]+1; parent[v]=u; push(q,v)

**Dijkstra (non-negative weights)**
    
    dist=default(INF); dist[src]=0; parent={src:None}; pq=[(0,src)]
    while pq:
        d,u = pop_min(pq)
        if d!=dist[u]: continue             # skip stale
        for (v,w) in adj[u]:
            nd = d+w
            if nd < dist[v]:
                dist[v]=nd; parent[v]=u; push(pq,(nd,v))

**0–1 BFS (weights ∈ {0,1})**
    
    dist=default(INF); dist[src]=0; dq=[src]
    while dq:
        u = pop_left(dq)
        for (v,w) in adj[u]:
            nd = dist[u]+w
            if nd < dist[v]:
                dist[v]=nd
                if w==0: push_left(dq,v) else: push_right(dq,v)

**Bellman–Ford (neg edges + cycle detect)**
    
    dist=default(INF); dist[src]=0
    repeat V-1 times:
        for (u,v,w) in edges:
            dist[v] = min(dist[v], dist[u]+w)
    neg_cycle = any(dist[u]+w < dist[v] for (u,v,w) in edges)

**Topological Sort (Kahn)**
    
    indeg = count_incoming(adj); q=[u for u in V if indeg[u]==0]; order=[]
    while q:
        u = pop_left(q); order.append(u)
        for v in adj[u]:
            indeg[v]-=1; if indeg[v]==0: push(q,v)
    # len(order) < V ⇒ cycle

**DAG Shortest Path**
    
    order = topo_sort()
    dist=default(INF); dist[src]=0
    for u in order:
        for (v,w) in adj[u]:
            dist[v] = min(dist[v], dist[u]+w)

**Prim (MST, undirected)**
    
    seen={start}; pq = edges_from(start); MST=[]; total=0
    while pq and len(seen)<V:
        (w,u,v)=pop_min(pq)
        if v in seen: continue
        MST.append((u,v,w)); total+=w; seen.add(v); push_all(pq, edges_from(v))

**Kruskal (MST, undirected)**
    
    sort edges by weight; MST=[]; total=0
    for (w,u,v) in edges:
        if find(u)!=find(v): union(u,v); MST.append((u,v,w)); total+=w

---

## 6) Tips

- **Pick by weights:** unweighted → BFS; {0,1} → 0–1 BFS; non-neg → Dijkstra; negatives → Bellman–Ford; DAG → topo+DP.
- **Path reconstruction:** keep `parent` when exploring/relaxing.
- **Dijkstra:** never with **negative** edges; always **skip stale** heap entries.
- **MST choice:** **Kruskal** for sparse; **Prim** (heap) for dense-ish.
- **Modeling trick:** grid/maze → node per cell; edges = valid moves (with costs).

---

## 7) Pitfalls

- Running **Dijkstra** on graphs with **negative weights**.
- Mixing **directed** and **undirected** edges accidentally.
- Assuming connectivity (Prim) on a **disconnected** graph.
- Skipping Bellman–Ford’s **final cycle check**.
- Expecting a topo order on a graph with a **cycle**.

---

## 8) Complexity Snapshot

| Task / Algorithm            | Time                      | Space |
|----------------------------|---------------------------|-------|
| BFS / DFS                  | **O(V + E)**              | O(V)  |
| Topological Sort           | **O(V + E)**              | O(V)  |
| Dijkstra (binary heap)     | **O((V + E) log V)**      | O(V)  |
| 0–1 BFS                    | **O(V + E)**              | O(V)  |
| Prim (binary heap)         | **O(E log V)**            | O(V)  |
| Kruskal (with DSU)         | **O(E log E)**            | O(V)  |
| Bellman–Ford               | **O(V · E)**              | O(V)  |
| Floyd–Warshall (all-pairs) | **O(V³)**                 | O(V²) |

**Rule of thumb:** start with BFS/DFS for structure; pick shortest-path/MST algorithms by **weight rules** and **graph type**.

---

# Intervals Pattern

**Intervals** describe spans on a line (time, coordinates). Most problems reduce to **sorting by start/end** and then either **greedy**, **sweep-line**, or **DP (weighted)**. Typical asks: **merge**, **detect conflicts**, **min rooms/resources**, **max compatible set**, **total covered length**, or **range updates/queries**.

---

## 1) Why & When

- **Scheduling / calendars:** merge busy slots, check conflicts, **min rooms** (Meeting Rooms).  
- **Pick max non-overlapping** activities (earliest finish first).  
- **Insert & merge** a new interval into a set.  
- **Intersections / overlaps** of two interval lists.  
- **Max concurrent / peak load**; **total coverage length**.  
- **Range add / difference array** (car-pooling, bookings).  
- **Weighted jobs** with start–end–profit (**weighted interval scheduling**).

**Key idea:** choose a **view** (sorted by start vs end; events for sweep; DP if profits) that turns the problem into one pass or simple recurrence.

---

## 2) Core Mental Model

- Intervals are `[start, end)` by default (half-open) unless told otherwise.  
- **Sort** once → linear scan.  
- **Greedy by earliest end** maximizes count of non-overlapping intervals.  
- **Sweep-line:** convert to **events** `(t, +1/-1)`; scan to track active count/coverage.  
- **Min-heap of end times** tracks ongoing intervals → size = rooms in use.  
- **DP with binary search** for profits: `dp[i] = max(dp[i-1], profit[i] + dp[prev_non_conflict(i)])`.

---

## 3) How to Recognize It

Phrases like:
- “**Merge** intervals / add a meeting”  
- “Minimum **rooms** / **resources** needed at once”  
- “**Max number of non-overlapping** events/jobs”  
- “**Total time covered** / **peak concurrent**”  
- “Given bookings (start,end,inc), is **capacity** exceeded?”  
- “Jobs with **start/end/profit**; pick a max-profit subset (no overlaps)”

---

## 4) Key Variants (Patterns)

- **Merge intervals:** sort by start; coalesce overlaps.  
- **Insert interval:** add one, then merge locally.  
- **Activity selection (max non-overlap):** sort by **end**, greedily keep compatible.  
- **Meeting rooms (min resources):** sweep events **or** min-heap of end times.  
- **Interval intersections:** two-pointer over two sorted lists.  
- **Total covered length / max overlap:** sweep with running `active`.  
- **Range add / capacity check:** **difference array** + prefix sum.  
- **Weighted interval scheduling:** DP + binary search for previous non-conflict.

---

## 5) Ultra-Compact Blueprints (Pseudo — copy-clean)

**Merge intervals (sort by start)**
    
    sort intervals by start
    out = []
    for (s,e) in intervals:
        if out empty or s > out[-1].end: out.append([s,e])
        else: out[-1].end = max(out[-1].end, e)

**Insert interval (then merge locally)**
    
    intervals.append(new)
    sort by start
    merge as above

**Max non-overlapping count (activity selection)**
    
    sort intervals by end
    count = 0; last_end = -inf
    for (s,e) in intervals:
        if s >= last_end:
            count += 1
            last_end = e

**Meeting Rooms — min number of rooms (two options)**

*Sweep (events)*:
    
    events = [(s,+1) for (s,e)] + [(e,-1) for (s,e)]
    sort events by (time, type)  # process -1 before +1 for [start,end)
    active = 0; peak = 0
    for (t,delta) in events:
        active += delta; peak = max(peak, active)
    # peak = rooms needed

*Min-heap of end times*:
    
    sort intervals by start
    heap = []  # end times
    for (s,e) in intervals:
        if heap and heap[0] <= s: pop(heap)   # room frees
        push(heap, e)
    rooms = size(heap)

**Intersections of two sorted lists**
    
    i=j=0; out=[]
    while i<len(A) and j<len(B):
        s = max(A[i].s, B[j].s)
        e = min(A[i].e, B[j].e)
        if s < e: out.append([s,e])           # use s<=e if closed intervals
        if A[i].e < B[j].e: i += 1
        else: j += 1

**Total covered length (union) via sweep**
    
    events = [(s,+1),(e,-1)] for each interval
    sort by (time, type)  # -1 before +1 at same time for [s,e)
    active = 0; prev = None; total = 0
    for (t,delta) in events:
        if active > 0: total += t - prev
        active += delta
        prev = t

**Range add / capacity check (difference array)**
    
    # bookings: add 'k' to [s,e) for each
    diff = Counter()
    for (s,e,k) in bookings:
        diff[s] += k; diff[e] -= k
    load = 0
    for t in sorted(diff):
        load += diff[t]
        if load > capacity: return False
    return True

**Weighted interval scheduling (max profit, no overlaps)**
    
    sort jobs by end time
    ends = [job[i].end for i]
    dp[0]=0
    for i in 1..n:
        j = rightmost index < i with ends[j] <= jobs[i].start  # binary search
        dp[i] = max(dp[i-1], jobs[i].profit + dp[j])
    # answer = dp[n]; reconstruct by standard DP traceback if needed

---

## 6) Tips

- **Half-open `[s, e)`** avoids fencepost bugs; if using **closed** `[s, e]`, adjust comparisons and event tie-breaks.  
- Be explicit about **tie-breaking**: for sweeps with `[s,e)`, process **end before start** at the same time.  
- For **min rooms**, sweep and heap approaches are equivalent; pick one.  
- **Sort once**; most solutions become linear after sorting.  
- **Coordinate compression** helps when endpoints are huge but few.  
- Always confirm **units/time zones** when dealing with real calendars.

---

## 7) Pitfalls

- Mixing **closed vs half-open** semantics → off-by-one overlaps.  
- Sorting by **start** when the greedy needs **end** (activity selection).  
- Forgetting to **pop all** heap ends ≤ current start (rooms).  
- Event **tie order** wrong → overcounts concurrent intervals.  
- Assuming merged input when it isn’t (insert must re-merge).  
- Weighted scheduling without **binary search** → O(n²) slowdowns.

---

## 8) Complexity Snapshot

| Problem / Pattern                          | Time                         | Space |
|--------------------------------------------|------------------------------|-------|
| Merge / Insert+Merge                       | **O(n log n)**               | O(1) or O(n) output |
| Max non-overlap (activity selection)       | **O(n log n)**               | O(1) |
| Meeting rooms (sweep or heap)              | **O(n log n)**               | O(n) |
| Intersections of two sorted lists          | **O(n + m)**                 | O(1) or O(k) output |
| Union length (sweep)                       | **O(n log n)**               | O(1) |
| Range add / capacity (difference array)    | **O(n log n)** (sort events) | O(n) |
| Weighted interval scheduling (DP+binary)   | **O(n log n)**               | O(n) |

**Rule of thumb:** if it’s about **compatibility or counts**, try **sort+greedy**; if it’s about **running totals/peaks**, use a **sweep**; if it’s about **profits**, reach for **DP with binary search**.

---

*End of Notes*

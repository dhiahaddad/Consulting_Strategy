# Data Structures Guide (Choosing the Right One)

This guide helps you choose data structures that make code faster, simpler, and less bug-prone—especially in Python-heavy research software.

---

## Quick Decision Table

### You need… fast membership tests / dedup
Use:
- `set` for membership + uniqueness
- `frozenset` for hashable/immutable sets

Avoid:
- `list` membership checks in hot loops (`x in list` is $O(n)$)

### You need… key → value lookup
Use:
- `dict`
- `collections.defaultdict` when you want a default value
- `collections.Counter` for counts

### You need… ordered sequence you index by position
Use:
- `list` for general purpose
- `tuple` for fixed-size / immutable records

### You need… queue / stack behavior
Use:
- `collections.deque` for FIFO queues ($O(1)$ append/pop both ends)
- `list` for LIFO stack (`append`/`pop` from end)

Avoid:
- `list.pop(0)` for queues (shifts elements; slow)

### You need… keep items sorted while inserting
Use:
- `bisect` on a sorted list (good for moderate sizes)
- `heapq` if you mostly need min/max and incremental updates

### You need… large numeric arrays / linear algebra
Use:
- `numpy.ndarray` (vectorized operations, compact storage)

Avoid:
- lists of lists for heavy numeric work

### You need… tabular labeled data
Use:
- `pandas.DataFrame` / `Series`

---

## Complexity Rules of Thumb

### `list`
- Indexing: $O(1)$
- Append at end: amortized $O(1)$
- Insert/remove in middle: $O(n)$
- Membership (`x in list`): $O(n)$

### `dict`
- Lookup/insert/delete: average $O(1)$
- Great for memoization, join-like operations, grouping

### `set`
- Membership: average $O(1)$
- Great for uniqueness and “have we seen this before?”

### `deque`
- `popleft()` / `appendleft()`: $O(1)$

### `heapq`
- Push/pop: $O(\log n)$
- Best for “always get smallest/largest next” scheduling

---

## Common Consulting Scenarios (With Recommended Structures)

### 1) “My loop is slow when checking if an item exists.”
**Symptom**: `if x in big_list:` in a loop.

**Fix**: use a `set` for membership.

```python
seen = set(big_list)
for x in queries:
    if x in seen:
        ...
```

### 2) “I keep searching for objects by ID.”
**Fix**: build an index `dict[id] -> object`.

```python
by_id = {item.id: item for item in items}
obj = by_id.get(target_id)
```

### 3) “I need to group rows by key.”
**Fix**: `defaultdict(list)`.

```python
from collections import defaultdict

groups = defaultdict(list)
for row in rows:
    groups[row.key].append(row)
```

### 4) “I need counts/frequencies.”
**Fix**: `Counter`.

```python
from collections import Counter
counts = Counter(labels)
```

### 5) “I’m building a BFS/queue.”
**Fix**: `deque`.

```python
from collections import deque
q = deque([start])
while q:
    node = q.popleft()
```

---

## Data Modeling: Records and Types

### Use `dataclass` for structured records
A `dataclass` communicates intent, reduces bugs, and improves readability.

```python
from dataclasses import dataclass

@dataclass
class Sample:
    id: str
    value: float
    condition: str
```

### Use tuples for lightweight, immutable records
Good when structure is small and stable.

---

## Research-Code Notes (Where People Get Tripped Up)

### Pandas vs NumPy
- If you need labels, joins, groupby, missing-data handling: pandas.
- If you need heavy numeric kernels: NumPy.
- Converting back and forth repeatedly can dominate runtime—choose a “core” representation and stick to it in hot paths.

### Don’t store huge numeric data in Python objects
- Python objects have overhead.
- Prefer `numpy.ndarray` for large numeric matrices and vectors.

### Beware nested dictionaries for “semi-structured” data
- They get hard to validate.
- Consider defining small dataclasses or using explicit schemas.

---

## Questions to Ask a Client (to choose correctly)
- “Do you need fast lookup by key, or ordered iteration?”
- “Is the data mostly static or frequently updated?”
- “How big is it now, and how big will it get?”
- “Is this numeric heavy (vectorize) or control-flow heavy (dict/set/deque)?”
- “Is correctness (units/shapes) a recurring issue? Should we model records explicitly?”

---

## Rule #1
If you’re unsure, measure.
- Start with the simplest structure.
- Add a micro-benchmark or profiler run.
- Change the structure only when the bottleneck is real.

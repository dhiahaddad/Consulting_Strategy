# Performance Optimization Guide (Research Software / Python)

Rule #1: optimize what you can measure. This guide focuses on reliable, low-risk performance wins.

---

## Performance Workflow
1. **Define the target**
   - Runtime goal (e.g., 10 min → 2 min)
   - Dataset size now vs expected future size
   - Constraints: memory limit, cores, GPU availability

2. **Measure baseline**
   - Time the full pipeline
   - Capture peak memory if possible

3. **Profile**
   - Find the top hotspots
   - Decide if you’re CPU-, memory-, or I/O-bound

4. **Choose the right lever**
   - Algorithm/data structure
   - Vectorization
   - Reduce I/O
   - Parallelism
   - Caching

5. **Verify correctness + performance**
   - Tests and/or output comparison
   - Avoid performance regressions with simple benchmarks

---

## Quick Wins (High ROI)

### 1) Avoid repeated I/O
- Read once, process in memory.
- Use efficient formats for large data (Parquet, HDF5) when appropriate.

### 2) Reduce Python-level loops
- Prefer NumPy operations, `numba`, or vectorized pandas patterns.

### 3) Avoid unnecessary copies
- Watch out for `df.copy()` everywhere.
- Be careful with conversions between pandas/NumPy.

### 4) Use better algorithms
Example: using a dictionary/map for lookups instead of `list.index` in loops.

---

## Profiling Toolkit

### Coarse timing
```python
import time
start = time.perf_counter()
run_pipeline()
elapsed = time.perf_counter() - start
print(f"elapsed={elapsed:.3f}s")
```

### Function-level profiling (standard library)
- `cProfile` is a good first step.

### Line-level / deeper analysis
- Use line profilers when hotspots are unclear.

---

## NumPy/Pandas Optimization Notes
- Prefer `numpy` arrays for heavy numeric loops.
- In pandas, avoid row-wise `apply` when possible.
- Use vectorized operations and boolean indexing.

Common anti-pattern:
```python
# slow: Python loop over rows
for _, row in df.iterrows():
    ...
```

---

## Parallelism: When It Helps
Parallelism helps if:
- Tasks are independent
- Computation per task is large relative to overhead
- You’re not bottlenecked by a single disk/network resource

### Multiprocessing pitfalls
- Large data transfer between processes can dominate runtime.
- Prefer chunking and shared read-only data patterns.

---

## HPC/Cluster Considerations
- Control BLAS/OpenMP thread counts to avoid oversubscription.
- Make memory usage predictable (avoid loading everything twice).
- Keep logs lean and structured.

---

## Performance Regression Guardrails
- Add a tiny benchmark or timing test (separate from unit tests if needed).
- Record baseline timings for a small representative dataset.

---

## What to Ask the Client
- “Where does the time go—data loading, preprocessing, compute, plotting?”
- “What is the largest dataset you expect in 6 months?”
- “Is the pipeline interactive or batch?”
- “Is accuracy/tolerance negotiable, or fixed?”

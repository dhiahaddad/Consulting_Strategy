# Refactoring Guide (Research Software / Python)

Refactoring is change that improves design without changing behavior. In research code, the safest path is incremental refactoring with small verification steps.

---

## Refactoring Principles
- **Measure risk**: refactor the smallest surface area that yields the biggest clarity win.
- **One behavior change at a time**: don’t mix “cleanup” with “new features”.
- **Lock behavior first**: add tests (or a baseline dataset + golden outputs) before big moves.
- **Make states explicit**: configs, randomness, and environment assumptions should be visible.

---

## A Safe Refactor Workflow
1. **Define invariants**
   - Inputs/outputs, units, shapes
   - Tolerances (numerical)
   - Performance constraints (runtime/memory)

2. **Add a safety net**
   - Unit tests for core functions
   - One integration “smoke test” for the pipeline
   - If no tests: snapshot outputs for a small fixture dataset

3. **Refactor in small steps**
   - Move code without changing logic
   - Rename variables for clarity
   - Extract functions
   - Introduce data structures (dataclass) for cohesion

4. **Verify after each step**
   - Run tests
   - Compare outputs
   - Check performance does not regress unexpectedly

5. **Finalize**
   - Remove dead code
   - Update docs/README
   - Add/change CI checks if needed

---

## High-Value Refactors in Research Code

### 1) Separate “library code” from “scripts/notebooks”
Goal: make core logic importable and testable.

Target structure:
- `src/` for reusable modules
- `scripts/` or CLI for runnable entrypoints
- `notebooks/` for exploration

### 2) Introduce a single entry point
Goal: one reliable way to run the pipeline.
- `python -m package.pipeline --config config.yaml`

### 3) Replace hidden globals with configuration
Common smell: many magic constants in the middle of the code.
- Use a config object/file (dataclass / YAML)
- Pass config into functions

### 4) Make data boundaries explicit
- Parse/validate inputs once
- Keep internal representation consistent (types, units)

---

## Code Smells Checklist
- [ ] Functions > ~50 lines doing multiple jobs
- [ ] Same code copied across scripts/notebooks
- [ ] Implicit assumptions (working directory, environment variables)
- [ ] Mixed concerns (I/O + computation + plotting in one function)
- [ ] Unclear naming (`data2`, `tmp`, `res_final_final`)
- [ ] Side effects hidden in helpers (mutating inputs)
- [ ] Tight coupling (everything imports everything)

---

## Refactor Patterns (With Examples)

### Extract function
Before: long block repeated in multiple places.
After: single function with clear inputs/outputs.

### Introduce dataclass for cohesion
```python
from dataclasses import dataclass

@dataclass(frozen=True)
class ExperimentConfig:
    alpha: float
    seed: int
    max_iter: int
```

### Pure functions where possible
- Prefer returning new objects over mutating inputs.
- Makes testing and debugging significantly easier.

---

## How to Refactor with Numerical Code
- Use tolerances: `np.allclose(a, b, rtol=..., atol=...)`
- Validate invariants: finite checks, shape checks
- Keep reference implementation (slow but clear) for small tests

---

## What to Ask the Client Before a Refactor
- “What behavior must not change (outputs, plots, metrics)?”
- “What is the smallest dataset we can use as a ‘golden’ fixture?”
- “Who runs this code and how (notebook, CLI, cluster)?”
- “Is there a deadline that limits how deep we can go?”

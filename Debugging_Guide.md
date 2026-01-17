# Debugging Guide (Research Software / Python)

This guide is designed for short, high-impact debugging during a 45‑minute consultation.

---

## Debugging Goals
- Reproduce the issue reliably
- Reduce it to a minimal reproducible example (MRE)
- Identify the root cause (not just symptoms)
- Add a regression test (so it stays fixed)

---

## The 10-Minute Triage
1. **Define the failure**
   - What is the *expected* behavior?
   - What is the *actual* behavior?
   - Is the failure deterministic?

2. **Capture the environment**
   - OS + Python version
   - Key package versions
   - How it’s run (CLI, notebook, cluster job, CI)

3. **Get the smallest repro**
   - Smallest input that triggers it
   - Minimal script/command
   - Save logs / stack trace

4. **Classify the bug** (pick one)
   - Environment/import
   - Data/IO
   - Logic/correctness
   - Numerical/stability
   - Concurrency/parallelism
   - Performance/timeout

---

## Minimal Reproducible Example (MRE) Checklist
- [ ] Single command to run
- [ ] Input data reduced to the smallest representative sample
- [ ] No external hidden state (working directory, global caches)
- [ ] No secrets
- [ ] Output shows expected vs actual clearly

---

## Fast Techniques That Work

### 1) Make the failure loud and local
- Replace “mysterious wrong output” with explicit **assertions**.
- Validate assumptions at boundaries (file read, parsing, shapes, units).

Example assertions:
```python
assert x.ndim == 2, f"Expected 2D array, got {x.ndim}D"
assert x.shape[0] == y.shape[0], "Mismatched samples"
assert np.isfinite(x).all(), "Found NaN/inf"
```

### 2) Print/log with intent
Prefer structured logging over scattered prints.

```python
import logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

logger.info("Loading %s", path)
logger.info("x.shape=%s y.shape=%s", x.shape, y.shape)
```

### 3) Bisect changes
If the bug appeared “recently”, use git to narrow the culprit.
- Identify last known good commit
- `git bisect` between good and bad

### 4) Divide and conquer
Comment out half of the pipeline, then re-enable step by step.
- If you can’t delete code, add feature flags to skip steps.

---

## Common Python Failure Modes

### Import / environment problems
Symptoms: `ModuleNotFoundError`, “works in notebook but not CLI”, wrong package version.

Actions:
- Confirm interpreter used by the failing run.
- Confirm installation mode (editable install for local packages).
- Print key metadata:

```python
import sys, platform
print(sys.executable)
print(sys.version)
print(platform.platform())
```

### File/Path issues
Symptoms: works from one directory, fails in CI/cluster.

Actions:
- Use absolute paths or paths relative to a known project root.
- Avoid implicit current working directory assumptions.

### Pandas/NumPy shape/align issues
Symptoms: silent broadcasting, wrong joins/alignments, “looks plausible but wrong”.

Actions:
- Check shapes and indexes at each boundary.
- Add unit tests on small fixtures.

### Floating point and numerical stability
Symptoms: slight differences, instability, NaNs after some iterations.

Actions:
- Use tolerances (`np.allclose`) not exact equality.
- Add checks for `NaN/inf` and scale/normalize where appropriate.

---

## Debugging in Notebooks vs Scripts
- Notebooks hide state; restart kernel to confirm reproducibility.
- Prefer a script/CLI entrypoint for the “real run”.
- Move logic into importable modules; keep notebooks thin.

---

## Regression Test Pattern (Recommended)
When fixed, lock it in:
- Add a test that fails before the fix and passes after.
- Keep it small and deterministic.

Example (pytest):
```python
def test_bug_123_regression():
    data = [0.0, 1.0, 2.0]
    result = function_under_test(data)
    assert result == expected
```

---

## What to Ask the Client (High Signal)
- “What’s the exact command you run to see the failure?”
- “Can we reduce the input to 10 lines/rows?”
- “Does it fail on a clean environment?”
- “When did it last work (commit/date)?”
- “Is there randomness, parallelism, or GPU involvement?”

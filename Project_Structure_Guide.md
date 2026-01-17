# Project Structure Guide (Organizing Folders and Files)

This guide gives practical folder/file organization patterns for research software projects, especially Python-heavy codebases.

---

## Goals of a Good Structure
- Make it obvious how to **run** the project
- Make it easy to **test** and refactor safely
- Separate **library code** from **experiments/notebooks**
- Keep **data** and **results** organized without accidentally committing them
- Support **reproducibility** (configs, environments)

---

## Recommended Baseline (Python Research Project)

```
project/
  README.md
  LICENSE
  pyproject.toml
  .gitignore
  src/
    package_name/
      __init__.py
      pipeline.py
      io.py
      models/
      utils/
  tests/
    test_pipeline.py
  scripts/
    run_pipeline.py
  notebooks/
    01_exploration.ipynb
  configs/
    default.yaml
  data/
    .gitkeep
  results/
    .gitkeep
  docs/
```

### What goes where
- `src/package_name/`: reusable, importable code (what you want to test)
- `scripts/`: thin entry points (CLI wrappers). Keep logic out of scripts.
- `notebooks/`: exploration only. Move stable code into `src/`.
- `configs/`: YAML/TOML configs for runs (tracked)
- `data/`, `results/`: local-only by default; add `.gitignore` rules
- `tests/`: unit/integration tests

---

## Rules of Thumb

### 1) Keep notebooks thin
- Notebooks should call functions from `src/`.
- If notebook cells contain lots of logic, that logic should become a module.

### 2) Separate I/O from compute
- `io.py`: parsing, loading, saving
- `pipeline.py`: orchestrating steps
- `models/` or `analysis/`: algorithms and math

This reduces bugs and makes testing far easier.

### 3) Use one “official” way to run
Examples:
- `python -m package_name.pipeline --config configs/default.yaml`
- `python scripts/run_pipeline.py --config configs/default.yaml`

Document it in the README.

### 4) Keep experiments reproducible
- Track configs in `configs/`
- Log parameters and output locations
- Prefer deterministic seeds when possible

### 5) Don’t commit large data/results by default
- Put raw data in `data/` and ignore it
- Put outputs in `results/` and ignore them
- Track only small fixtures needed for tests

---

## `.gitignore` Recommendations
Common patterns:
- `data/`
- `results/`
- `*.csv`, `*.h5`, `*.parquet` (if large)
- `__pycache__/`, `.pytest_cache/`, `.mypy_cache/`
- `venv/`, `.venv/`
- Notebook checkpoints: `.ipynb_checkpoints/`

---

## Naming Conventions
- Use **nouns** for modules that hold concepts (`io.py`, `pipeline.py`, `metrics.py`)
- Use **verbs** for scripts/CLIs (`run_pipeline.py`, `download_data.py`)
- Keep package name stable and importable (avoid spaces/casing changes)

---

## When to Introduce More Structure

### Add `docs/` when
- You have users beyond yourself
- You need “how to run” instructions and a methods explanation

### Add `scripts/` when
- You run multiple repeatable tasks (download, preprocess, train, evaluate)

### Add `src/package_name/cli.py` when
- You want a stable CLI entry point

---

## What to Ask a Client (to choose the right structure)
- “Is this a one-off analysis, or will it be reused for months?”
- “Is there a single pipeline, or many independent scripts?”
- “Will others install this (package) or just run scripts?”
- “Do you need to publish/release this (paper/package)?”

---

## Checklist (Quick Audit)
- [ ] README has one copy-paste command to run the main workflow
- [ ] Core logic is in `src/`, not in notebooks
- [ ] `tests/` exists and runs in CI
- [ ] `configs/` holds tracked run configs
- [ ] `data/` and `results/` are ignored or clearly managed

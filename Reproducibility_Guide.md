# Reproducibility Guide (Environments, Dependencies, and Runs)

This guide focuses on the practical steps that make research software reproducible for you, collaborators, and reviewers.

---

## Reproducibility Checklist (Minimum Viable)
- [ ] One command to run the main workflow (documented in `README.md`)
- [ ] Dependencies captured (and ideally pinned)
- [ ] Configs captured (parameters are not hidden in notebooks)
- [ ] Randomness controlled (seeds + notes on nondeterminism)
- [ ] A small “smoke test” dataset and/or integration test

---

## Choose a Reproducibility Path

There isn’t one perfect toolchain. Pick a path that matches your constraints, then implement it consistently.

Good defaults:
- **Pure Python, simple installs** → pip + venv + pinned `requirements.txt`
- **Pure Python, strict locking** → pip-tools or Poetry/uv
- **Scientific stack / compiled deps / HPC** → conda/mamba (`environment.yml`)
- **Need OS-level reproducibility** (system libs, CUDA, exact runtime) → Docker (optionally with a devcontainer)

---

## Dependencies: `requirements.txt` vs `pyproject.toml`

### When to use `requirements.txt`
Use it when:
- You have a script-style project or internal analysis
- You want the simplest possible setup (`pip install -r requirements.txt`)

Best practice:
- Pin versions if the goal is reproducibility (especially for papers):
  - Good: `numpy==1.26.4`
  - Risky: `numpy>=1.20`

### When to use `pyproject.toml`
Use it when:
- You want an installable package (`pip install .`)
- You want clean imports, testing, and CI
- You expect other people to reuse the code over time

Best practice:
- Declare your project metadata and dependencies in `pyproject.toml`
- Install in editable mode during development: `pip install -e .`

### Recommendation (typical research code)
- If it’s meant to be reused: **package it** (`pyproject.toml`) and keep scripts thin.
- If it’s a short-lived analysis: a pinned `requirements.txt` can be sufficient.

---

## Path A: pip + virtual environment + `requirements.txt` (Simple, Works Everywhere)

**Commit to git**:
- `requirements.txt` (prefer pinned for papers)
- `README.md` setup instructions

**Create and activate venv**:
```bash
python -m venv .venv

# Windows
.venv\Scripts\activate

# macOS/Linux
source .venv/bin/activate
```

**Install deps**:
```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

**Pros**: minimal, low ceremony, easy for collaborators.

**Cons**: pinning can be manual; OS-level deps (BLAS/CUDA) may differ across machines.

---

## Path B: pip-tools (`requirements.in` → locked `requirements.txt`) (Great for “paper-grade” locking)

**Commit to git**:
- `requirements.in` (your top-level deps)
- `requirements.txt` (compiled lock)

**Workflow**:
```bash
pip install pip-tools

# create/edit requirements.in, then compile
pip-compile requirements.in

# install exact locked set
pip-sync requirements.txt
```

**`requirements.in` example**:
```text
numpy
pandas
scipy
matplotlib
pytest
```

**Pros**: deterministic, still uses pip; easy diff in PRs.

**Cons**: doesn’t capture OS/system libs.

---

## Path C: `pyproject.toml` package + pip (Installable project)

Use this when you want reliable imports, a clean `src/` layout, and CI-friendly installs.

**Commit to git**:
- `pyproject.toml`
- `src/…`
- `tests/…`

**Typical developer install**:
```bash
python -m venv .venv
source .venv/bin/activate  # or Windows activate
pip install -e .
```

**Pros**: clean architecture, easier testing/CI, fewer “works in notebook only” issues.

**Cons**: slightly more setup; still not full OS-level reproducibility.

---

## Path D: Poetry (`pyproject.toml` + `poetry.lock`) (Convenient dependency + lock management)

**Commit to git**:
- `pyproject.toml`
- `poetry.lock`

**Typical workflow**:
```bash
poetry install
poetry run pytest
```

**Pros**: lockfile by default, nice workflow.

**Cons**: adds a tool requirement; some HPC environments prefer conda.

---

## Path E: uv (Fast pip-compatible workflows)

uv is a fast Python package manager that can accelerate installs and support locked workflows.

**Practical use**:
- Use uv to speed up pip-style installs.
- Keep committed artifacts simple (`requirements.txt` / `pyproject.toml`) so collaborators aren’t forced into one tool.

---

## Path F: conda/mamba (`environment.yml`) (Best for scientific stacks and HPC)

**Commit to git**:
- `environment.yml`

**Create env**:
```bash
conda env create -f environment.yml
conda activate your-env-name
```

**Typical `environment.yml` pattern**:
- Pin Python
- Pin key scientific libs
- Prefer `conda-forge` for consistency

**Pros**: handles compiled libs well; reduces “pip build failures”; common on clusters.

**Cons**: env solving can be slow; lockfiles vary by platform.

---

## Path G: Docker (OS-level reproducibility)

Use Docker when you need a reproducible runtime including system packages and native libs.

**Commit to git**:
- `Dockerfile`
- (optional) `docker-compose.yml`

**Typical pattern**:
- Base image pinned (Python version)
- System deps installed explicitly
- Python deps installed from pinned requirements/lockfile

**Pros**: strongest reproducibility; great for deployment and complex stacks.

**Cons**: requires Docker; HPC may require Singularity/Apptainer instead.

---

## Path H: Devcontainer (VS Code) for consistent dev environments

A devcontainer gives collaborators the same tooling and dependencies in VS Code.

**Commit to git**:
- `.devcontainer/devcontainer.json`
- (optional) `.devcontainer/Dockerfile`

**Pros**: consistent setup for contributors; reduces onboarding time.

**Cons**: still depends on Docker; not always feasible in restricted environments.

---

## Locking Dependencies (Important for Papers)
Pinning is good; a lockfile is better.

Options:
- **pip-tools**: maintain `requirements.in` → compile to locked `requirements.txt`
- **Poetry**: `pyproject.toml` + `poetry.lock`
- **uv** (fast pip replacement): supports locked workflows (tooling evolves quickly)
- **Conda/Mamba**: `environment.yml` (often best for compiled scientific stacks)

Rule of thumb:
- If you depend on compiled libs (BLAS/MKL/CUDA): consider conda/mamba.
- If pure Python + PyPI: pip-tools/poetry/uv are fine.

---

## Conda Environment (`environment.yml`)
Use conda for scientific stacks, HPC, or when pip builds are painful.

Typical pattern:
- Include Python version, core libs, and channels.
- Export a minimal `environment.yml` for sharing.

---

## Python Package Project Layout (for reproducibility)
- Keep code in `src/` so imports reflect the installed package.
- Add a CLI/script entry point that takes a config file.

---

## Configuration Management

### Don’t hardcode parameters in notebooks
- Store run parameters in `configs/*.yaml` (or TOML/JSON)
- Log the exact config used for each run into the output folder

### Record “run metadata”
At minimum, record:
- git commit hash
- config file
- environment info (Python + key deps)

Practical approach:
- Write a small helper that prints versions and saves them next to outputs.
- Store `config.yaml` (or equivalent) inside each run folder.

---

## Randomness and Determinism

### Seeds
- Set seeds for Python, NumPy, and any ML framework you use.
- Document what “reproducible” means (bitwise identical vs statistically similar).

### Nondeterminism sources
- Multithreading and BLAS libraries
- GPU kernels
- Floating point order-of-operations

Best practice:
- Use tolerance-based comparisons for numeric results (`np.allclose`).

---

## Data and Outputs

### Don’t commit large datasets by default
- Keep raw data out of git (`data/` ignored)
- Include small test fixtures only (safe, tiny)

### Make outputs traceable
- Use run folders: `results/YYYY-MM-DD_run-name/`
- Save configs + logs next to outputs

---

## Reproducibility for Reviewers
A strong “review-ready” setup typically includes:
- `README.md` with setup + one command to run
- Locked dependencies (lockfile or pinned requirements)
- Small demo dataset or synthetic generator
- CI that runs tests and a smoke pipeline

If you can’t ship data:
- Include a synthetic-data generator that produces representative shapes/distributions.
- Document expected runtime and hardware.

---

## What to Ask a Client
- “Is this for a paper submission (strict reproducibility) or internal use (pragmatic)?”
- “Does anyone else need to run it on different machines?”
- “Do you use GPUs/HPC (nondeterminism, compiled deps)?”
- “Can we create a tiny representative dataset for tests?”

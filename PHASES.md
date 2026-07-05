# Colabs — Phased Repository Evolution

This repository uses a **trunk-based checkpoint model** to document the progression
of the project phase by phase. Each phase is committed to `main`, then sealed with
a **tag** and a **snapshot branch** so anyone can checkout that exact moment in
the project's history.

---

## How the Checkpoint Model Works

```
main (trunk) ──────────────────────────────────────────────►
              │                   │                   │
              ▼                   ▼                   ▼
           phase-1             phase-2             phase-3
           phase/1             phase/2             phase/3
           (tag + branch)      (tag + branch)      (tag + branch)
```

| Artefact         | Purpose                                                          |
| ---------------- | ---------------------------------------------------------------- |
| `main` branch    | Always reflects the current / latest state of the project        |
| `phase/N` branch | Permanent snapshot of `main` at the close of Phase N             |
| `phase-N` tag    | Immutable annotated tag pointing to the same commit as `phase/N` |

### Checking Out a Phase

```bash
# See Phase 1 state (branch)
git checkout phase/1

# Or checkout via tag
git checkout phase-1

# Return to latest
git checkout main
```

---

## Phase Log

### Phase 1 — Configure Python, uv, Jupyter & Google Colab

> **Branch:** `phase/1`  **Tag:** `phase-1`

**Goal:** Establish a reproducible local Python environment that mirrors the
Google Colab runtime for data-science work using `pandas`, `numpy`, `matplotlib`,
`jupyterlab`, and `ipykernel`.

**What was added / configured:**

| File / Folder                                      | Purpose                                                            |
| -------------------------------------------------- | ------------------------------------------------------------------ |
| `pyproject.toml`                                   | Project metadata, Python `>=3.13` constraint, core dependencies    |
| `uv.lock`                                          | Pinned dependency lockfile for reproducible installs               |
| `.python-version`                                  | Pins the Python version for `uv` and `pyenv`                       |
| `.gitignore`                                       | Ignores `.venv`, `.ai-chats`, `.waylog`, Jupyter checkpoints, etc. |
| `docs/README.md`                                   | Project overview                                                   |
| `docs/tutorials/setup-uv-jupyter-colab.md`         | Step-by-step: set up `uv` + Jupyter + Colab parity locally         |
| `docs/how-to/connect-notebook-to-colab-runtime.md` | How to connect a local `.ipynb` to a remote Colab runtime          |
| `docs/how-to/troubleshoot-uv-jupyter-colab.md`     | Common issues and fixes                                            |
| `docs/reference/python-colab-packages.md`          | Reference table: Colab packages vs local equivalents               |
| `Colabs.code-workspace`                            | VS Code workspace file                                             |
| `.github/`                                         | Copilot instructions, skills, prompts for this repo                |

**Dependencies installed (via `uv sync`):**

```toml
dependencies = [
    "ipykernel>=6.29",     # Jupyter kernel for VS Code notebooks
    "ipywidgets>=8.1",     # Interactive widgets (matches Colab)
    "jupyterlab>=4.2",     # Full JupyterLab UI
    "matplotlib>=3.9.2",   # Plotting (mirrors Colab)
    "notebook>=7.2",       # Classic notebook server
    "numpy>=2.1",          # Array computing (mirrors Colab)
    "pandas>=2.2.3",       # DataFrames (mirrors Colab)
]
```

**Dev tools:**

```toml
[dependency-groups]
dev = ["ruff>=0.6"]        # Linting + formatting, incl. .ipynb support
```

---

### Phase 2 — *(planned)*

> TBD

---

### Phase 3 — *(planned)*

> TBD

---

## Tagging & Branching Conventions

```bash
# After completing a phase and committing to main:
git tag -a phase-N -m "Phase N: <short description>"
git branch phase/N
git push origin main --tags
git push origin phase/N
```

Tags are **annotated** (`-a`) so they carry a message and are visible in
GitHub Releases and `git log --decorate`.

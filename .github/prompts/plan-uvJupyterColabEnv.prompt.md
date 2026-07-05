# Plan: uv + Jupyter + Google Colab environment for `Colabs`

Set up a reproducible local Python 3.12 environment in `d:\code\Colabs` managed entirely by **uv** (project mode with `pyproject.toml` + `uv.lock`), register it as a Jupyter kernel, wire up the Google Colab VS Code extension for remote runtimes, confirm/declare extensions, research conservative Colab-friendly packages, and document it all as a Diátaxis Tutorial + How-to + Reference set. The workspace is greenfield for Python (no `.vscode/`, no notebooks, no `pyproject.toml`), so nothing existing will be overwritten.

**Note on Colab integration:** the `google-colab` pip package is **Colab-runtime-only** and cannot be installed locally. Local "integration" is delivered two ways: (a) the **Google.colab** VS Code extension connecting local `.ipynb` files to a remote Colab GPU/TPU runtime via the kernel picker, and (b) local Google Drive/Sheets client libraries (deferred per package-scope decision). This is called out explicitly so no step attempts an impossible install.

## Phase 1 — uv environment (foundation)

1. Verify uv is global: `uv --version`; verify Python 3.12 availability via `uv python list` / `uv python install 3.12`.
2. Initialise the project: `uv init --python 3.12` at the workspace root → generates `pyproject.toml`; then `uv python pin 3.12` → writes `.python-version`. Remove the sample `main.py`/`hello.py` uv scaffolds if created.
3. Establish `.gitignore` entries for `.venv/`, `__pycache__/`, `.ipynb_checkpoints/`.

### 1a — Ruff dev tooling (lint + format parity)

- **Decision: include Ruff as a dev-only dependency.** `uv add --dev ruff` adds it under `[dependency-groups] dev` (or `[tool.uv] dev-dependencies`) in `pyproject.toml` — it stays out of the runtime dependency set and is never shipped to a Colab runtime.
- **Editor integration:** recommend the `charliermarsh.ruff` VS Code extension (declared in Phase 4 `extensions.json`) so lint + format run in-editor against the same binary uv resolves.
- **Config block in `pyproject.toml`** — conservative, notebook-aware defaults:
  - `[tool.ruff]` → `line-length = 88`, `target-version = "py312"`.
  - `[tool.ruff.lint]` → a modest rule set (`E`, `F`, `I`, `W`, `UP`, `B`) — no aggressive opinionated rules that fight notebook style.
  - Enable notebook linting: `[tool.ruff]` → `extend-include = ["*.ipynb"]` so `.ipynb` cells are checked/formatted too.
- **Invocation (uv-native, no global install):** `uv run ruff check .` and `uv run ruff format .`; ephemeral one-offs via `uvx ruff check`. No bare `pip install ruff`.
- **Fallback note.** If you want strictly-minimal tooling, Ruff can be dropped without affecting the environment — it is orthogonal to the Jupyter/Colab path.

## Phase 2 — Jupyter + kernel (*depends on Phase 1*)

4. Add Jupyter stack: `uv add jupyterlab notebook ipykernel ipywidgets` (creates `.venv` + `uv.lock` automatically).
5. Discover existing kernels: `uv run jupyter kernelspec list` — inventory what's already registered.
6. Register this venv as a selectable kernel: `uv run python -m ipykernel install --user --name colabs-uv312 --display-name "Colabs (uv · Py3.12)"` so VS Code's kernel picker and existing Jupyter installs can reuse it.

### 2a — Kernel reuse strategy (`--user`, cross-tool visibility)

- **Decision: register `--user`, not project-local.** A `--user` kernelspec lands in the per-user Jupyter data dir (`jupyter --data-dir` → `kernels/colabs-uv312/`), making it selectable from **any** Jupyter/JupyterLab/VS Code/Colab-bridge install on this machine — not just when a shell is `cwd`'d into `d:\code\Colabs`.
- **Existing-kernel inventory first (step 5).** Capture `jupyter kernelspec list` output *before* registering so we can detect and reuse any pre-existing `python3`/data-science kernels rather than proliferating near-duplicates. If a suitable existing kernel already points at a compatible interpreter, note it in the Reference doc as an alternative selection.
- **Stable naming contract.** `--name colabs-uv312` is the immutable machine id (lowercase, hyphenated, version-suffixed); `--display-name "Colabs (uv · Py3.12)"` is the human label shown in the picker. Re-running the install command is idempotent — it overwrites the same kernelspec in place, so upgrades (e.g. a 3.13 bump → `colabs-uv313`) never collide.
- **Interpreter pinning inside the kernelspec.** The generated `kernel.json` hard-codes the absolute `.venv` interpreter path, so the kernel keeps resolving even when launched from outside the project folder. If `.venv` is ever recreated, re-run step 6 to refresh the path.
- **Removal path (documented, not executed):** `uv run jupyter kernelspec uninstall colabs-uv312` cleanly de-registers without touching the venv.
- **Fallback note.** If you later prefer isolation over visibility, drop `--user` for a project-scoped kernelspec (via `--prefix .venv`) — recorded in the Reference doc as the alternative, but not the default.

## Phase 3 — Google Colab integration (*parallel with Phase 4*)

7. Declare the **Google.colab** extension (remote runtime bridge, requires `ms-toolsai.jupyter`); document Auto-Connect vs New Colab Server (GPU/TPU) flow.
8. Document — but do **not** yet install — the deferred local Drive/Sheets libs (`google-api-python-client`, `google-auth-oauthlib`, `gspread`, `pydrive2`) as a "add later" block, per the core-only scope decision.

## Phase 4 — VS Code config

9. Create `.vscode/extensions.json` (recommendations: `GitHub.copilot`, `GitHub.copilot-chat`, `ms-toolsai.jupyter`, `ms-python.python`, `Google.colab`, `charliermarsh.ruff`).
10. Create `.vscode/settings.json` (interpreter → `.venv`, `python.terminal.activateEnvironment`, jupyter kernel defaults, notebook line numbers/output scrolling).
11. Confirm installed extensions: `code --list-extensions` and diff against recommendations; surface any missing to you (no auto-install).

## Phase 5 — Package research (deliverable, *parallel with Phase 4*)

12. Produce a curated, sourced Reference table of conservative high-repute Colab-compatible packages (core installed now; ML/data-IO/Drive tiers marked "add later"), pinning rationale to Colab parity.

## Phase 6 — Documentation via documentation-writer / Diátaxis (*depends on all prior phases*)

### 6a — Docs root scaffolding (Diátaxis layout)

- **Decision: create a top-level `docs/` root** in `d:\code\Colabs` (workspace currently has none). This keeps published guidance discoverable and separate from `.notes/` (which holds working/source notes, including the reference input doc).
- **Directory layout — one folder per Diátaxis quadrant:**
  ```
  docs/
  ├── README.md          ← index: maps the four quadrants + links each doc
  ├── tutorials/         ← learning-oriented (Action × Acquisition)
  ├── how-to/            ← task-oriented (Action × Application)
  ├── reference/         ← information-oriented (Cognition × Application)
  └── explanation/       ← (reserved — no doc authored this pass)
  ```
- **`docs/README.md` index** — a short Diátaxis compass map with a one-line description and link per document; the single entry point a reader lands on. No quadrant content itself, only navigation.
- **`explanation/` reserved but empty** — created as a placeholder so the four-quadrant structure reads intentionally; no Explanation doc is written this pass (would be the natural home for a future "About local↔Colab runtime parity" piece).
- **Cross-linking rule:** each doc links *across* to sibling quadrants rather than embedding their content (blur prevention) — e.g. the Tutorial links to the How-to for the Drive-mount recipe and to the Reference for the full package table.
- **Fallback note.** If you prefer to keep everything under `.notes/`, the same four subfolders can be nested there instead; `docs/` is the recommended default for reader-facing material.

13. **Tutorial** — `docs/tutorials/setup-uv-jupyter-colab.md`: guided first-run, one happy path to a working kernel.
14. **How-to** — `docs/how-to/connect-notebook-to-colab-runtime.md`: task recipe for connecting an `.ipynb` to a Colab GPU runtime and mounting Drive.
15. **Reference** — `docs/reference/python-colab-packages.md`: the package table from step 12 + uv command reference + settings schema. Cross-linked, no quadrant blur.

## Relevant files (all new — nothing overwritten)

- `pyproject.toml` — uv project + `[dependency-groups] dev` (ruff) + `[tool.ruff]` config (steps 2, 4, 1a)
- `.python-version`, `uv.lock`, `.venv/` — uv project (steps 2, 4)
- `.gitignore` — ignore venv/checkpoints (step 3)
- Kernelspec `~/…/jupyter/kernels/colabs-uv312/kernel.json` — `--user` kernel (step 6, 2a)
- `.vscode/settings.json` — interpreter + jupyter config (step 10)
- `.vscode/extensions.json` — recommended extensions incl. `charliermarsh.ruff` (step 9, 1a)
- `docs/README.md` — Diátaxis index/compass map (step 6a)
- `docs/tutorials/setup-uv-jupyter-colab.md` — Diátaxis Tutorial (step 13)
- `docs/how-to/connect-notebook-to-colab-runtime.md` — Diátaxis How-to (step 14)
- `docs/reference/python-colab-packages.md` — Diátaxis Reference (step 15)
- `docs/explanation/` — reserved empty folder (step 6a)
- Reference doc `.notes/Configure/GitHub Copilot Plan Mode…md` — source input for the docs (read-only)

## Verification

1. `uv run python -c "import sys; print(sys.version)"` → reports 3.12.x.
2. `uv run python -c "import numpy, pandas, matplotlib; print('ok')"` → imports succeed.
3. `uv run jupyter kernelspec list` → shows `colabs-uv312`.
4. `uv run jupyter lab --version` → JupyterLab resolves.
5. Open a scratch `.ipynb`, confirm VS Code kernel picker lists "Colabs (uv · Py3.12)" and the **Colab** kernel option (extension present).
6. `code --list-extensions` includes `Google.colab` and `ms-toolsai.jupyter`.
7. Diátaxis self-check: each doc answers exactly one Compass quadrant; cross-links instead of embedding foreign-quadrant content.

## Decisions (from clarifications)

- Python **3.12**; uv **project mode** (`pyproject.toml` + `uv.lock`); **core-only** packages now (`jupyter`, `ipykernel`, `numpy`, `pandas`, `matplotlib`), ML/Drive libs documented as deferred; docs = **Tutorial + How-to + Reference**.
- uv is the sole package/CLI manager: `uv add` for deps, `uv run`/`uvx` for execution, `uv tool install` for global CLIs — no bare `pip`/`pipx`.
- **Kernel reuse (§2a):** register `--user` (cross-tool visibility) with stable id `colabs-uv312`; inventory existing kernels first; idempotent re-install; documented uninstall path.
- **Ruff (§1a):** included as a **dev-only** dependency (`uv add --dev ruff`) + `charliermarsh.ruff` extension + notebook-aware `[tool.ruff]` config; run via `uv run ruff` — droppable if strictly minimal is preferred.
- **Docs root (§6a):** create top-level `docs/` with one folder per Diátaxis quadrant + `docs/README.md` compass index; `explanation/` reserved empty.
- **Excluded:** installing `google-colab` locally (impossible); ML/Drive package installation (deferred); creating actual notebook content beyond a verification scratch file.

## Verification additions

- `uv run ruff check .` and `uv run ruff format --check .` → exit clean (or report only intended findings).
- `uv run jupyter kernelspec list` → `colabs-uv312` resolves from a shell **outside** `d:\code\Colabs` (confirms `--user` visibility).
- `docs/README.md` present and links all four quadrant folders; `docs/explanation/` exists (empty).

## References

- <https://colab.research.google.com/#scrollTo=Wf5KrEb6vrkR>
- `.notes/Configure/GitHub Copilot Plan Mode — Workspace Configuration for Notebooks & Google Colab.md`

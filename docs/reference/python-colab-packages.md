# Reference — Python & Colab packages, commands, and settings

Factual reference for the `Colabs` environment. Consult it; do not read it end to
end. For a guided setup, see the [tutorial](../tutorials/setup-uv-jupyter-colab.md).

---

## Package tiers

Packages are grouped by whether they are installed now (core) or documented for later
addition. Version constraints are conservative floors chosen for Google Colab parity;
verify against a live Colab runtime with `!pip list` when exact matching matters.

### Installed now (core)

| Package      | Constraint | Role                         |
| ------------ | ---------- | ---------------------------- |
| `jupyterlab` | `>=4.2`    | Notebook IDE / server        |
| `notebook`   | `>=7.2`    | Classic notebook front-end   |
| `ipykernel`  | `>=6.29`   | Kernel that backs the venv   |
| `ipywidgets` | `>=8.1`    | Interactive notebook widgets |
| `numpy`      | `>=2.0`    | Numerical arrays             |
| `pandas`     | `>=2.2`    | Data frames                  |
| `matplotlib` | `>=3.9`    | Plotting                     |

### Dev tooling

| Package | Constraint | Role                                                               |
| ------- | ---------- | ------------------------------------------------------------------ |
| `ruff`  | `>=0.6`    | Lint + format (dev-only group; also `.ipynb` via `extend-include`) |

### Deferred (add later)

Not installed in the core setup. Add with `uv add <pkg>` when a task needs them.

| Tier                  | Packages                                                                                 | When to add                                          |
| --------------------- | ---------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| Machine learning      | `scikit-learn`, `scipy`, `seaborn`                                                       | Modelling / statistical plots                        |
| Data I/O              | `pyarrow`, `openpyxl`, `requests`                                                        | Parquet, Excel, HTTP downloads                       |
| Google Drive / Sheets | `google-api-python-client`, `google-auth`, `google-auth-oauthlib`, `gspread`, `pydrive2` | Reaching Drive/Sheets from the **local** environment |

### Not installable locally

| Package        | Reason                                                                                                                                                                                                                                                            |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `google-colab` | Pre-installed **only** inside a Colab runtime. Provides `from google.colab import drive`. Attempting `uv add google-colab` / `pip install google-colab` locally will not give you a usable local Colab bridge — use the `Google.colab` VS Code extension instead. |

---

## uv command reference

All package and execution operations go through uv. Do not use bare `pip` or `pipx`.

| Command                           | Effect                                                    |
| --------------------------------- | --------------------------------------------------------- |
| `uv sync`                         | Create/update `.venv` and `uv.lock` from `pyproject.toml` |
| `uv add <pkg>`                    | Add a runtime dependency and install it                   |
| `uv add --dev <pkg>`              | Add a dev-only dependency (e.g. `ruff`)                   |
| `uv remove <pkg>`                 | Remove a dependency                                       |
| `uv run <cmd>`                    | Run a command inside the environment                      |
| `uvx <cmd>`                       | Run a tool ephemerally without adding it to the project   |
| `uv tool install <cli>`           | Install a global CLI tool managed by uv                   |
| `uv python pin 3.13`              | Pin the project interpreter (writes `.python-version`)    |
| `uv python list --only-installed` | List installed Python versions                            |

### Kernel management

| Command                                                                                               | Effect                                         |
| ----------------------------------------------------------------------------------------------------- | ---------------------------------------------- |
| `uv run python -m ipykernel install --user --name colabs-uv313 --display-name "Colabs (uv · Py3.13)"` | Register the venv as a user-wide kernel        |
| `uv run jupyter kernelspec list`                                                                      | List all registered kernels                    |
| `uv run jupyter kernelspec uninstall colabs-uv313`                                                    | De-register the kernel (leaves `.venv` intact) |

- The kernel `--name` (`colabs-uv313`) is the immutable machine id; the
  `--display-name` is the human label in the picker.
- Registration is idempotent — re-running overwrites the same kernelspec in place.
- A `--user` kernel is visible to every Jupyter/VS Code/Colab-bridge install on the
  machine. For a project-local kernel instead, replace `--user` with `--prefix .venv`.

### Ruff

| Command                        | Effect                            |
| ------------------------------ | --------------------------------- |
| `uv run ruff check .`          | Lint all `.py` and `.ipynb` files |
| `uv run ruff format .`         | Format in place                   |
| `uv run ruff format --check .` | Verify formatting without writing |

---

## VS Code settings schema

Defined in `.vscode/settings.json` for the `Colabs` folder.

| Key                                   | Value                                         | Purpose                                     |
| ------------------------------------- | --------------------------------------------- | ------------------------------------------- |
| `python.defaultInterpreterPath`       | `${workspaceFolder}/.venv/Scripts/python.exe` | Point VS Code at the uv venv (Windows path) |
| `python.terminal.activateEnvironment` | `true`                                        | Auto-activate the venv in new terminals     |
| `notebook.lineNumbers`                | `"on"`                                        | Show line numbers in cells                  |
| `notebook.output.scrolling`           | `true`                                        | Scroll long cell output                     |
| `notebook.formatOnSave.enabled`       | `true`                                        | Format notebooks on save                    |
| `ruff.nativeServer`                   | `"on"`                                        | Use the Ruff native language server         |
| `[python].editor.defaultFormatter`    | `charliermarsh.ruff`                          | Ruff formats Python                         |

## Recommended extensions

Defined in `.vscode/extensions.json`.

| Extension ID          | Role                                               |
| --------------------- | -------------------------------------------------- |
| `GitHub.copilot`      | AI completions                                     |
| `GitHub.copilot-chat` | AI chat                                            |
| `ms-python.python`    | Python language support                            |
| `ms-toolsai.jupyter`  | Notebook editing + kernels                         |
| `Google.colab`        | Remote Colab runtimes (needs `ms-toolsai.jupyter`) |
| `charliermarsh.ruff`  | Lint + format                                      |

Confirm what is installed with `code --list-extensions`.

---

## Sources

- Google Colab: <https://colab.research.google.com/>
- uv documentation: <https://docs.astral.sh/uv/>
- Google Colab VS Code extension:
  <https://marketplace.visualstudio.com/items?itemName=Google.colab>

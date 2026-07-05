# Troubleshooting & gotchas: uv, Jupyter, and Colab

This guide collects the real errors hit while setting up this environment, with the
symptom, the cause, and the fix for each. Scan for your error message.

It assumes the setup in the [tutorial](../tutorials/setup-uv-jupyter-colab.md). For
command and package details, see the [reference](../reference/python-colab-packages.md).

---

## `AssertionError: SRE module mismatch` on any import

**Symptom**

```text
Invalid -W option ignored: invalid action: 'info'
...
  File "D:\Langs\Python\313\Lib\re\_compiler.py", line 18, in <module>
    assert _sre.MAGIC == MAGIC, "SRE module mismatch"
AssertionError: SRE module mismatch
```

`python -c "import sys; print(sys.version)"` reports one version (e.g. `3.12.x`) but
the traceback loads stdlib from a **different** version's folder (e.g.
`D:\Langs\Python\313\Lib`).

**Cause**

A global `PYTHONHOME` (and/or `PYTHONPATH`) environment variable points at a Python
install that differs from the interpreter running. The interpreter binary loads a
foreign standard library, and the compiled `_sre` magic number no longer matches.

**Fix**

Clear the polluting variables, then rebuild the environment so binary and stdlib
match:

```powershell
Remove-Item Env:PYTHONHOME     -ErrorAction SilentlyContinue
Remove-Item Env:PYTHONPATH     -ErrorAction SilentlyContinue
Remove-Item Env:PYTHONWARNINGS -ErrorAction SilentlyContinue
Remove-Item -Recurse -Force .venv
uv sync
```

To make it permanent, clear them at the OS level and open a fresh terminal:

```powershell
[Environment]::SetEnvironmentVariable('PYTHONHOME', $null, 'User')
# repeat with 'Machine' scope (elevated shell) if they are set there too
```

**Lesson:** never set a global `PYTHONHOME`. Let uv own the interpreter per project
via `.python-version` and `.venv`.

---

## `Invalid -W option ignored: invalid action: 'info'`

**Symptom**

Every Python invocation prints this line before running.

**Cause**

A `PYTHONWARNINGS=info` environment variable. `info` is not a valid warning action
(valid: `error`, `ignore`, `always`, `default`, `module`, `once`).

**Fix**

Remove it (session and permanently):

```powershell
Remove-Item Env:PYTHONWARNINGS -ErrorAction SilentlyContinue
[Environment]::SetEnvironmentVariable('PYTHONWARNINGS', $null, 'User')
```

---

## `warning: Resolving despite existing lockfile due to fork markers being disjoint`

**Symptom**

```text
warning: Resolving despite existing lockfile due to fork markers being disjoint
with `requires-python`: `python_full_version == '3.12.*'` vs
`python_full_version == '3.13.*'`
```

**Cause**

`uv.lock` was generated under an older `requires-python` (here `3.12.*`), but
`pyproject.toml` now targets a different range (`3.13.*`). The lockfile's Python
markers no longer overlap the project's, so uv re-resolves on every command.

**Fix**

Regenerate the lockfile to match the new `requires-python`:

```powershell
uv lock
uv sync
```

**Lesson:** whenever you change `requires-python` (or bump the Python version), run
`uv lock` immediately so the lockfile and project stay in sync.

---

## `Failed to hardlink files; falling back to full copy`

**Symptom**

```text
Failed to hardlink files; falling back to full copy. This may lead to degraded
performance. ... set `UV_LINK_MODE=copy` or use `--link-mode=copy` to suppress.
```

**Cause**

uv's package cache and the project's `.venv` are on **different drives/filesystems**
(here the cache is on `C:` and the project on `D:`). Hardlinks cannot span
filesystems, so uv copies instead and warns.

**Fix (project-scoped, recommended)**

Declare copy mode in `pyproject.toml` — committed, so it applies for everyone:

```toml
[tool.uv]
link-mode = "copy"
```

**Fix (performance alternative)**

Put the cache on the same drive as your projects to restore hardlinking, then you can
drop `link-mode`:

```powershell
[Environment]::SetEnvironmentVariable('UV_CACHE_DIR', 'D:\.uv-cache', 'User')
```

**Lesson:** keep the uv cache and your `.venv` on the same drive for best
performance; otherwise set `link-mode = "copy"` to silence the noise.

---

## `google-colab` will not install locally

**Symptom**

`uv add google-colab` (or `pip install google-colab`) fails or yields a package that
does not provide `from google.colab import drive` locally.

**Cause**

`google-colab` is pre-installed **only** inside a Colab runtime. It is not a
general-purpose PyPI package for local environments.

**Fix**

Do not install it locally. Use the **Google.colab** VS Code extension to run a
notebook against a remote Colab runtime (see
[Connect a notebook to a Colab runtime](connect-notebook-to-colab-runtime.md)). For
local Drive access, use the deferred Google client libraries listed in the
[reference](../reference/python-colab-packages.md#deferred-add-later).

---

## Local files are missing on a Colab runtime

**Symptom**

Code that reads `./data/file.csv` works locally but raises `FileNotFoundError` after
switching the kernel to a Colab runtime.

**Cause**

The Colab runtime is a remote machine. Your local working directory is not mounted
there.

**Fix**

Mount Google Drive or upload the file to the runtime:

```python
from google.colab import drive
drive.mount("/content/drive")
# then read from /content/drive/MyDrive/...
```

---

## The kernel disappears after recreating `.venv`

**Symptom**

`Colabs (uv · Py3.13)` no longer runs cells, or points at a missing interpreter,
after you deleted and rebuilt `.venv`.

**Cause**

The kernelspec's `kernel.json` hard-codes the absolute interpreter path. Recreating
`.venv` can invalidate it.

**Fix**

Re-register the kernel (idempotent — overwrites in place):

```powershell
uv run python -m ipykernel install --user --name colabs-uv313 --display-name "Colabs (uv · Py3.13)"
```

---

## Related

- [Set up uv + Jupyter + Colab](../tutorials/setup-uv-jupyter-colab.md)
- [Connect a notebook to a Colab runtime](connect-notebook-to-colab-runtime.md)
- [Python & Colab packages reference](../reference/python-colab-packages.md)

# Set up uv + Jupyter + Google Colab

In this tutorial, we will build a working local Python 3.13 environment for Jupyter
notebooks, managed entirely by [uv](https://docs.astral.sh/uv/), and register it so
VS Code and Google Colab can both use it. By the end you will have run a notebook cell
locally and be ready to connect the same notebook to a Colab GPU runtime.

We will do this one step at a time. Each step produces a visible result — check it
before moving on.

## Before we start

You need:

- **uv** installed and on your `PATH`. Check with `uv --version`. If it prints a
  version number, you are ready. (If not, install it from
  <https://docs.astral.sh/uv/getting-started/installation/>.)
- **VS Code** with the extensions this workspace recommends. When you open the folder,
  VS Code will offer to install them — accept. We will confirm them at the end.

Open a terminal in the project folder `d:\code\Colabs`. Every command below is run
from there.

## Step 1 — Create the environment

The project already contains a `pyproject.toml` describing the dependencies. Ask uv to
build the environment from it:

```powershell
uv sync
```

uv will create a `.venv/` folder, download Python 3.13 if needed, install the
packages, and write a `uv.lock` file. You will see a summary ending with a list of
installed packages such as `jupyterlab`, `numpy`, `pandas`, and `matplotlib`.

Let's check the Python version uv chose:

```powershell
uv run python -c "import sys; print(sys.version)"
```

The output should start with `3.13`. Good — the environment exists.

## Step 2 — Confirm the core libraries import

```powershell
uv run python -c "import numpy, pandas, matplotlib; print('ok')"
```

You should see a single line: `ok`. That confirms the data-science core is installed
and importable.

## Step 3 — Register the Jupyter kernel

So that VS Code and any Jupyter install on your machine can select this environment,
register it as a named kernel:

```powershell
uv run python -m ipykernel install --user --name colabs-uv313 --display-name "Colabs (uv · Py3.13)"
```

Now list the kernels to confirm it appears:

```powershell
uv run jupyter kernelspec list
```

You will notice `colabs-uv313` in the list. This kernel is visible system-wide, not
only inside this folder.

## Step 4 — Run a notebook cell

Create a new file called `scratch.ipynb` in the project folder and open it in VS Code.
In the top-right kernel picker, choose **Colabs (uv · Py3.13)**.

In the first cell, type:

```python
import matplotlib
print("Local kernel is live:", matplotlib.__version__)
```

Run the cell. The output should print a matplotlib version number. You have executed
Python locally through the kernel you just registered.

## Step 5 — Confirm the extensions

Open the Extensions view and confirm these are installed:

- **Python** (`ms-python.python`)
- **Jupyter** (`ms-toolsai.jupyter`)
- **Google Colab** (`Google.colab`)

The Google Colab extension is what lets the *same* notebook run on a remote Colab
runtime — we set that up in a separate guide.

## What you have built

You now have a reproducible uv-managed Python 3.13 environment, a registered Jupyter
kernel, and a notebook running against it locally. Well done.

## Where to next

- To run this notebook on a Colab GPU/TPU instead of locally, follow
  [Connect a notebook to a Colab runtime](../how-to/connect-notebook-to-colab-runtime.md).
- For the full list of packages, uv commands, and VS Code settings, see the
  [Python & Colab packages reference](../reference/python-colab-packages.md).

# Getting Started — Complete Beginner's Guide

This guide is for anyone who is new to Python, Jupyter notebooks, and the command
line. It walks you through every step needed to get this project running on your
computer, from installing the tools to opening your first notebook.

You do not need any prior experience. Every command is explained in plain English.

---

## What is this project?

This repository gives you a **local Python environment** that works the same way
as [Google Colab](https://colab.research.google.com/) — the free, browser-based
notebook tool many people use for data science and machine learning.

By the end of this guide you will be able to:

- Write and run Python code in a notebook on your own computer.
- Use the same data-science libraries (pandas, numpy, matplotlib) that Colab provides.
- Optionally connect your local notebook to a Colab GPU if you need more power.

The project is built in **stages (phases)**. Each phase adds something new, so you can
follow along at your own pace and see how a real project evolves over time.

---

## What you will install

| Tool            | What it does                                      | Free? |
| --------------- | ------------------------------------------------- | ----- |
| **Git**         | Downloads (clones) this project to your computer  | Yes   |
| **Python 3.13** | The programming language                          | Yes   |
| **uv**          | Installs and manages Python packages for you      | Yes   |
| **VS Code**     | A friendly code editor (optional but recommended) | Yes   |

---

## Step 1 — Install Git

Git is how you download and keep up-to-date with this project.

**Windows:**
1. Go to <https://git-scm.com/download/win>
2. Download the installer and run it. Accept all the defaults.
3. When it finishes, open **Command Prompt** or **PowerShell** and type:

   ```text
   git --version
   ```

   You should see something like `git version 2.45.0`. If you do, Git is installed.

**Mac:**
Open **Terminal** and type `git --version`. If Git is not installed, macOS will
offer to install it automatically — click Install.

**Linux:**
```bash
sudo apt install git        # Ubuntu / Debian
sudo dnf install git        # Fedora
```

---

## Step 2 — Install Python 3.13

**Windows / Mac:**
1. Go to <https://www.python.org/downloads/>
2. Download the latest **Python 3.13** installer.
3. Run the installer. **Important on Windows:** tick the box that says
   *"Add Python to PATH"* before clicking Install.
4. Verify: open a new terminal and type:

   ```text
   python --version
   ```

   You should see `Python 3.13.x`.

**Linux:**
```bash
sudo apt install python3.13   # Ubuntu 24.04+
```

> **Tip:** If you already have a different Python version, do not worry — `uv`
> (installed next) will manage the exact version this project needs automatically.

---

## Step 3 — Install uv

`uv` is a fast, modern tool for managing Python packages. Think of it as a smarter
replacement for `pip install`.

Open a terminal and run:

**Windows (PowerShell):**
```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

**Mac / Linux:**
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Close your terminal and reopen it, then verify:

```text
uv --version
```

You should see something like `uv 0.4.x`.

---

## Step 4 — Get the project

This step downloads the project files to your computer.

Open a terminal, navigate to a folder where you want to keep projects (for example
your Documents folder), and run:

```bash
git clone https://github.com/iPoetDev/Colabs.git
cd Colabs
```

- `git clone` downloads all the files.
- `cd Colabs` moves you into the downloaded folder.

You should now see files like `pyproject.toml` and a `docs/` folder if you run
`ls` (Mac/Linux) or `dir` (Windows).

---

## Step 5 — Install the Python packages

This project uses pandas, numpy, JupyterLab, and other libraries. `uv` will install
them all in one command:

```bash
uv sync
```

`uv` reads the `pyproject.toml` file (which lists every package and its version) and
installs exactly those versions into a `.venv` folder inside the project. This keeps
everything isolated — it will not affect any other Python projects on your computer.

The first run may take a minute or two. You will see progress lines as packages are
downloaded.

**What does "success" look like?**
The command finishes without any red error text. You should see something like:

```text
Resolved 45 packages in ...ms
Installed 38 packages in ...ms
```

---

## Step 6 — Start JupyterLab

JupyterLab is a web-based notebook editor that runs on your computer.

```bash
uv run jupyter lab
```

- `uv run` activates the project environment automatically.
- `jupyter lab` starts the JupyterLab server.

Your default web browser should open automatically to an address like
`http://localhost:8888`. If it does not, copy the URL printed in the terminal
(it starts with `http://127.0.0.1:8888`) and paste it into your browser.

You will see the JupyterLab file browser. You are ready to create notebooks!

**To stop JupyterLab:** go back to the terminal and press `Ctrl + C`.

---

## Step 7 — Create your first notebook (optional)

1. In the JupyterLab file browser, click the **+** button (New Launcher).
2. Under *Notebook*, click **Python 3 (ipykernel)**.
3. A new notebook opens. Click into the first cell and type:

   ```python
   import pandas as pd
   import numpy as np

   data = {"name": ["Alice", "Bob"], "score": [95, 82]}
   df = pd.DataFrame(data)
   print(df)
   ```

4. Press **Shift + Enter** to run the cell.

If you see a table printed below the cell, everything is working correctly.

---

## Following along with the phases

This project is split into **phases** — each one adds something new. You can
"rewind" to any phase and see exactly what the project looked like at that moment.

To see a list of phases and what each one covers, read [PHASES.md](../../PHASES.md).

### Checking out a specific phase

```bash
# See Phase 1 (the current and only phase so far)
git checkout phase/1

# Return to the latest version
git checkout main
```

Think of each phase like a chapter bookmark — you can jump to any chapter and look
around, then return to the latest at any time.

---

## Common problems

### "command not found: uv"

Close your terminal and open a new one. The installer needs a fresh terminal to
update your PATH. If it still fails, restart your computer.

### "python: command not found" on Mac/Linux

Try `python3 --version` instead. On some systems Python 3 is available as
`python3`. The `uv` tool handles this automatically inside the project.

### JupyterLab does not open in the browser

Copy the full URL from the terminal output (look for a line starting with
`http://127.0.0.1:8888`) and paste it manually into your browser.

### Port 8888 is already in use

Another Jupyter instance may already be running. Either close it, or start on a
different port:

```bash
uv run jupyter lab --port 8889
```

---

## What's next?

Once your environment is working, explore the rest of the docs:

- [Full setup tutorial](../tutorials/setup-uv-jupyter-colab.md) — deeper walkthrough of every configuration choice
- [Connect to a Colab runtime](connect-notebook-to-colab-runtime.md) — run your local notebook on a Colab GPU
- [Troubleshooting](troubleshoot-uv-jupyter-colab.md) — fixes for common errors
- [Package reference](../reference/python-colab-packages.md) — full list of available libraries

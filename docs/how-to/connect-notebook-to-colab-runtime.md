# How to connect a notebook to a Google Colab runtime

This guide shows you how to run a local `.ipynb` file on a remote Google Colab
runtime (CPU, GPU, or TPU) from VS Code, and how to reach your Google Drive files
from that runtime.

It assumes you already have a working local environment (see the
[setup tutorial](../tutorials/setup-uv-jupyter-colab.md)) and the **Google Colab**
extension (`Google.colab`) installed.

## Prerequisites

- The `ms-toolsai.jupyter` extension (the Colab extension is built on top of it).
- The `Google.colab` extension.
- A Google account with Colab access.

## Connect to a Colab runtime

1. Open your `.ipynb` file in VS Code.
2. Click **Select Kernel** in the top-right of the notebook.
3. Choose **Colab**.
4. To get a default CPU runtime quickly, choose **Auto Connect**. To choose GPU or
   TPU, choose **New Colab Server** and pick the runtime type.
5. Sign in with your Google account when prompted.

The kernel indicator now shows the Colab runtime. Cells you run execute remotely.

> **Important:** the Colab runtime is a remote machine. Local files such as
> `./data/*.csv` are **not** visible to it. To use data, mount Google Drive or upload
> files explicitly (below).

## Mount Google Drive from the runtime

To read and write files on your Drive from a Colab runtime, run this in a cell:

```python
from google.colab import drive
drive.mount("/content/drive")
```

Follow the authorisation prompt. Your Drive is then available under
`/content/drive/MyDrive/`. For example:

```python
import pandas as pd
df = pd.read_csv("/content/drive/MyDrive/data/housing.csv")
```

## Switch back to the local kernel

To return to running locally, open the kernel picker again and select
**Colabs (uv · Py3.13)**. Nothing on the remote runtime is lost until it times out.

## If you need Drive access from your *local* environment

The `google.colab` module only exists inside a Colab runtime — it cannot be installed
locally. To reach Drive from your local Python instead, add the Google client
libraries (see the deferred "Google Drive / Sheets" tier in the
[reference](../reference/python-colab-packages.md#deferred-add-later)) and use
`pydrive2` or `google-api-python-client` with OAuth credentials.

## Related

- [Set up uv + Jupyter + Colab](../tutorials/setup-uv-jupyter-colab.md) — the one-time
  environment setup.
- [Python & Colab packages reference](../reference/python-colab-packages.md) — exact
  package names and install commands.

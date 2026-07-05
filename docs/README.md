# Colabs Documentation

Documentation for setting up and using a local Python + Jupyter environment with
Google Colab, organised by the [Diátaxis](https://diataxis.fr/) framework. Each
document serves exactly one reader need — start with the one that matches yours.

## The four quadrants

| Quadrant                        | Need                                                    | Document                                                                             |
| ------------------------------- | ------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| **Tutorial** (learning)         | *"Teach me to set this up from scratch."*               | [Set up uv + Jupyter + Colab](tutorials/setup-uv-jupyter-colab.md)                   |
| **How-to** (a task)             | *"How do I connect a notebook to a Colab GPU runtime?"* | [Connect a notebook to a Colab runtime](how-to/connect-notebook-to-colab-runtime.md) |
| **How-to** (fix an error)       | *"How do I resolve setup errors and gotchas?"*          | [Troubleshooting & gotchas](how-to/troubleshoot-uv-jupyter-colab.md)                 |
| **Reference** (facts)           | *"What are the packages, commands, and settings?"*      | [Python & Colab packages](reference/python-colab-packages.md)                        |
| **Explanation** (understanding) | *"Why is local ≠ Colab?"*                               | *Reserved — not yet written.*                                                        |

## Reading order

1. New here → follow the **Tutorial** once, end-to-end.
2. Environment already working → jump to the **How-to** for specific tasks.
3. Hit an error → check [Troubleshooting & gotchas](how-to/troubleshoot-uv-jupyter-colab.md).
4. Need exact package names, versions, or command syntax → consult the **Reference**.

> Documents link *across* quadrants rather than repeating each other. If a Tutorial
> step needs a full option table, it links to the Reference instead of embedding it.

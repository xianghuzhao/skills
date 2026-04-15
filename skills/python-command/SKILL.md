---
name: python-command
description: Use before running python or pip command.
---

# Run python script with uv

* Do not run python command with system python.
* **Never** install python package with system pip command.

uv is an extremely fast Python package and project manager. It replaces pip,
pip-tools, pipx, pyenv, virtualenv, poetry, etc.

## Setup python virtual environment

Before run any python command, use uv to setup python environment first.

```bash
uv venv 
uv pip install package
```

## Python command

Run python command with uv:

```bash
uv run python -V
uv run python -c 'import package'
```

## Python script

Run python script with uv:

```bash
uv run script.py
```

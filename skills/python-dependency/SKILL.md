---
name: python-dependency
description: Use before adding, removing, or editing Python dependencies in pyproject.toml files. Always prefer `uv add` / `uv remove` over hand-editing pyproject.toml.
---

# Python Dependency Management with uv

Use this workflow whenever a task requires changing Python dependency metadata.

## Core Rules

- Do **not** add a dependency unless code or tests in the project will use it.
- Do **not** edit `pyproject.toml` dependency entries by hand. Use `uv add` and `uv remove` instead.
- Preserve the project's existing style, including grouping, comments, and formatting in non-dependency sections of `pyproject.toml`.
- Put dependencies in the narrowest correct section: main dependencies in `[project.dependencies]`, dev/test dependencies in `[dependency-groups]` or `[tool.uv.dev-dependencies]`.

## Adding Dependencies

1. Inspect the project layout first: check `pyproject.toml` and any workspace or multi-project structure.
2. Use `uv add <package>` to add a main dependency:

```bash
uv add requests
```

3. Use `uv add --dev <package>` for dev/test-only dependencies:

```bash
uv add --dev pytest
```

4. Use `uv add --group <group> <package>` for named dependency groups when the project uses them:

```bash
uv add --group docs sphinx
```

5. To add a dependency to a specific project in a uv workspace:

```bash
uv add <package> --package <project-name>
```

## Removing Dependencies

- Remove a dependency only after confirming it is no longer imported by source, tests, or scripts.
- Use `uv remove <package>`:

```bash
uv remove requests
```

- Use `uv remove --dev <package>` for dev dependencies:

```bash
uv remove --dev pytest
```

## Versions and Extras

- Let `uv add` resolve the best compatible version unless the user requests a specific version:

```bash
uv add "requests>=2.31"
uv add "requests==2.31.0"
```

- Add optional dependency extras with bracket notation:

```bash
uv add "fastapi[standard]"
```

- If a package is already present and needs a version bump or extra flags, re-run `uv add` with the new spec — uv will update the existing entry.

## Validation

After dependency changes, run the smallest useful verification:

```bash
uv lock
uv run python -c "import <package>"
```

For broader checks:

```bash
uv sync
uv run pytest
```

If validation cannot run because dependencies cannot be downloaded or the environment blocks network access, report that explicitly.

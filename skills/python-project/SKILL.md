---
name: python-project
description: Use when developing, structuring, refactoring, testing, or reviewing Python application projects, especially uv-managed apps, async-capable services, CLIs, workers, or tools. Apply this skill for Python project layout, runtime architecture, async boundaries, testing, type checking, linting, dependency choices, and app-quality implementation guidance.
---

# Python Project Development

Guidelines for building maintainable Python application projects with `uv`, pragmatic async design, and modern quality tooling.

## Core Principles

1. **Use `uv` for project operations** - Run Python, tests, dependency changes, locks, and scripts through `uv`.
2. **Prefer typed, explicit boundaries** - Keep I/O, configuration, and side effects visible at module boundaries.
3. **Use async where it pays off** - Prefer async for I/O-heavy app code, but keep simple scripts and CPU-bound work straightforward.
4. **Make code testable first** - Structure app logic so tests can call it without launching the whole runtime.
5. **Follow the existing project** - Preserve established layout, framework, and conventions unless the user asks for a change.

## Project Layout

For new application projects, prefer a `src/` layout:

```text
pyproject.toml
src/
  my_app/
    __init__.py
    main.py
    config.py
    logging.py
tests/
```

- Put importable app code under `src/<package>/`.
- Put tests under `tests/`, mirroring important app modules when useful.
- Keep scripts thin; move reusable behavior into importable modules.
- Avoid relying on the working directory or ad hoc `sys.path` edits.

## uv Workflow

- Use `uv init --package` for new application projects so `uv` scaffolds package metadata and a `src/` layout.
- After initializing a project with `uv init`, add this Pyright virtual environment configuration to `pyproject.toml`:

  ```toml
  [tool.pyright]
  venvPath = "."
  venv = ".venv"
  ```

- Use `uv add` and `uv remove` for dependency metadata changes.
- Use `uv run` for Python commands, CLIs, tests, type checks, and linters.
- Use `uv lock` after dependency metadata changes when the project keeps a lockfile.
- Do not use system `python`, system `pip`, or manual dependency edits in `pyproject.toml`.

Common commands:

```bash
uv init --package my-app
uv run python -m my_app
uv run pytest
uv run ruff check .
uv run ruff format .
uv run pyright
```

## Async Patterns

- Prefer `async def` for I/O-heavy workflows such as HTTP calls, database access, queues, subprocess orchestration, and concurrent service operations.
- Keep sync code for small scripts, pure computation, startup parsing, and libraries without useful async APIs.
- Use one clear async entrypoint for apps instead of scattering event-loop management across modules.
- Do not call blocking I/O directly from async request handlers or tasks; use async clients or offload blocking work with `asyncio.to_thread` or an executor.
- Reuse long-lived clients where appropriate, such as HTTP or database clients with connection pools.
- Make cancellation and shutdown explicit for background tasks.

Example:

```python
import asyncio


async def run() -> None:
    ...


def main() -> None:
    asyncio.run(run())


if __name__ == "__main__":
    main()
```

## Framework Defaults

Use existing project choices first. For new app projects with no established framework:

- Use FastAPI for HTTP APIs.
- Use Typer for CLIs with more than trivial argument parsing.
- Use `httpx.AsyncClient` for async HTTP.
- Use standard-library `asyncio` unless the chosen framework or dependency already standardizes on AnyIO.

Add dependencies only when code or tests will use them. Keep framework-specific code near the app boundary and keep core logic framework-light.

## Configuration, Logging, and Errors

- Read configuration once at startup and pass typed settings into app construction.
- For non-trivial app configuration, prefer `pydantic-settings` with a small `BaseSettings` model in `config.py`.
- Keep environment parsing out of business logic.
- Use structured logging fields for request IDs, job IDs, user IDs, and external service names when those exist.
- Raise specific exceptions inside domain code and translate them at the app boundary.
- Include actionable context in error messages without leaking secrets.
- Keep dependency injection simple: pass clients, settings, and repositories through constructors or function parameters before introducing a container.

## Typing and Data Modeling

- Use precise type hints for public functions, app boundaries, and test helpers.
- Prefer dataclasses or Pydantic models for structured data crossing I/O boundaries.
- Avoid untyped dictionaries for long-lived internal state when a named type would make invalid states harder to express.
- Keep `Any`, casts, and `# type: ignore` local and justified.
- Validate external input at the boundary; avoid repeated defensive validation in internal code.

## Testing

- Use `pytest` by default.
- Test core logic without starting servers, workers, or real external services.
- Use async test support when testing async functions; prefer existing project fixtures and plugins.
- Mock at app boundaries, not deep inside implementation details.
- Cover configuration parsing, error translation, retries/timeouts, and cancellation behavior when relevant.
- Add regression tests for bug fixes before or alongside the fix.

## Quality Checks

Prefer this baseline for new projects:

```bash
uv add --dev pytest ruff pyright
uv run ruff format .
uv run ruff check .
uv run pyright
uv run pytest
```

When editing an existing project, run the smallest useful checks first, then broaden if the change affects shared behavior. Report clearly if checks cannot run because dependencies, network access, or environment setup are unavailable.

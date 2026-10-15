# Skills

Custom skills collection. Mainly for my personal use.

## Setup

Link the `skills` directory to your AI tool's skill location:

**Claude Code:**
```bash
ln -s "$(pwd)/skills" ~/.claude/skills
```

**Codex:**
```bash
ln -s "$(pwd)/skills" ~/.agents/skills/mine
```

## Skills

| Skill | Description |
|-------|-------------|
| [cargo-toml-file](skills/cargo-toml-file/) | Safely add, remove, or edit Cargo.toml dependencies |
| [code-simplification](skills/code-simplification/) | Simplify working code without changing behavior |
| [frontend-design](skills/frontend-design/) | Build and style distinctive, production-grade web interfaces |
| [python-command](skills/python-command/) | Run Python scripts and manage packages with uv |
| [python-dependency](skills/python-dependency/) | Manage Python dependencies in `pyproject.toml` with uv |
| [python-project](skills/python-project/) | Build, refactor, test, and review Python application projects |
| [rust-best-practices](skills/rust-best-practices/) | Develop Rust applications with async I/O, personal stack defaults, workspace conventions, and focused validation |
| [tiger-docs](skills/tiger-docs/) | Resolve decisions and write approved specifications in dev-docs covering implementation requirements and plans for documentation changes; explicit invocation only |
| [tiger-implement](skills/tiger-implement/) | Execute code and documentation tasks after approval of a complete file-change proposal, updating behavior documentation after corresponding implementation verification; explicit invocation only |
| [tiger-tasks](skills/tiger-tasks/) | Turn a specification into approved code and documentation tasks with verification dependencies, preserving task IDs and progress; explicit invocation only |

## Contributor Notes

See [AGENTS.md](AGENTS.md) for repository guidelines, validation commands, and agent-specific instructions.

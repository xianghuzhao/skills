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
| [grill-me](skills/grill-me/) | Stress-test a plan by relentless interviewing until shared understanding |
| [code-simplification](skills/code-simplification/) | Simplify working code without changing behavior |
| [python-command](skills/python-command/) | Run Python scripts and manage packages with uv |
| [python-dependency](skills/python-dependency/) | Manage Python dependencies in `pyproject.toml` with uv |
| [python-project](skills/python-project/) | Build, refactor, test, and review Python application projects |
| [rust-best-practices](skills/rust-best-practices/) | Rust coding best practices for idiomatic, efficient, and maintainable code |
| [cargo-toml-file](skills/cargo-toml-file/) | Safely add, remove, or edit Cargo.toml dependencies |
| [tiger-prd](skills/tiger-prd/) | Create lean PRDs from feature ideas or product plans |
| [tiger-design](skills/tiger-design/) | Create technical design docs from Tiger PRDs |
| [tiger-tasks](skills/tiger-tasks/) | Create implementation task lists from PRDs and designs |
| [tiger-implement](skills/tiger-implement/) | Implement Tiger task checklists |


## Contributor Notes

See [AGENTS.md](AGENTS.md) for repository guidelines, validation commands, and agent-specific instructions.

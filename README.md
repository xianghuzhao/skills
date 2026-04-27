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
| [python-command](skills/python-command/) | Run Python scripts and manage packages with uv |
| [rust-best-practices](skills/rust-best-practices/) | Rust coding best practices for idiomatic, efficient, and maintainable code |
| [cargo-toml-file](skills/cargo-toml-file/) | Safely add, remove, or edit Cargo.toml dependencies |
| [grill-me](skills/grill-me/) | Stress-test a plan by relentless interviewing until shared understanding |


## Creating a Skill

Each skill is a directory containing a `SKILL.md` file with frontmatter:

```yaml
---
name: my-skill
description: Short description of what the skill does.
allowed-tools: Read, Edit, Write, Bash, Grep, Glob, Task
---

# Skill Title

Skill instructions and guidelines...
```

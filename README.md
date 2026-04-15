# Skills

Claude Code custom skills collection.

## Usage

Copy skill directories into `~/.claude/skills/` to make them available in Claude Code.

## Skills

| Skill | Description |
|-------|-------------|
| [python-project](python-project/) | Python project and virtualenv management with uv |
| [rust-best-practices](rust-best-practices/) | Rust coding best practices for idiomatic, efficient, and maintainable code |

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

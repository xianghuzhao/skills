# Repository Guidelines

## Project Structure & Module Organization

This repository stores reusable AI agent skills. Root-level files are documentation: `README.md` explains setup and available skills, while `CLAUDE.md` points Claude Code at this guide.

Each skill lives in `skills/<skill-name>/SKILL.md`. A skill file starts with YAML frontmatter containing at least `name` and `description`, followed by Markdown instructions. Use lowercase kebab-case for skill directory names, matching the `name` field, for example `skills/python-project/SKILL.md`.

## Build, Test, and Development Commands

There is no build step or package manager for this repository. Useful maintenance commands are:

```bash
find skills -maxdepth 2 -name SKILL.md | sort
```

Lists all skill entrypoints.

```bash
rg -n "^name:|^description:|^allowed-tools:" skills
```

Checks skill metadata quickly.

## Coding Style & Naming Conventions

Write skills in Markdown with concise, action-oriented instructions. Keep headings descriptive and use fenced code blocks for examples. Frontmatter should remain valid YAML, and skill names should be stable lowercase kebab-case identifiers.

Prefer ASCII unless a skill specifically requires other characters. Keep examples practical and scoped to the skill; avoid adding unrelated policy or tool guidance.

## Testing Guidelines

Validate skills by reading them in the target agent and checking that trigger descriptions are specific enough to avoid accidental activation. Before committing, run the metadata search above and inspect any changed `SKILL.md` for malformed frontmatter, broken relative paths, or stale command examples.

If a skill references scripts, assets, or templates, verify those files exist relative to the skill directory.

## Commit & Pull Request Guidelines

Recent commits use short imperative subjects such as `Add tiger-skills`, `New python project`, and `Update readme`. Follow that style: keep the subject brief, capitalized, and focused on the changed skill or doc.

Pull requests should describe the skill or documentation change, list affected paths, and mention any manual validation performed. For new skills, include the intended trigger behavior and an example task that should activate the skill.

## Agent-Specific Instructions

Do not edit unrelated skills while updating one skill. Preserve existing frontmatter fields unless the change requires them. When adding a skill, update `README.md` so the public skill table stays aligned with `skills/`.

Do not add `agents/openai.yaml` unless the user explicitly asks for it.

---
name: tiger-tasks
description: Create unchecked implementation task lists from PRDs and optional design docs, writing docs/NNNN-name/tasks.md.
---

# Tiger Tasks

Use this skill to create an implementation task checklist from a PRD created by `tiger-prd` and, when present, a sibling design doc created by `tiger-design`.

Documentation-only scope: write or update the task list document only. Do not implement the feature, change product code, add tests, install dependencies, run migrations, or modify non-task files as part of this skill.

## Workflow

1. Find the source folder.
   - Use a user-provided `prd.md`, `design.md`, `tasks.md`, or `docs/NNNN-name/` path when present.
   - Otherwise infer the feature name from the chat and search for matching `docs/NNNN-*/prd.md` files.
   - If there is exactly one plausible match, use its parent folder.
   - If there are multiple plausible matches or none, ask one concise clarifying question and include a recommended choice.

2. Read source documents and inspect the project.
   - Always read `prd.md` from the source folder.
   - Read sibling `design.md` when it exists.
   - Inspect manifests, config files, source layout, tests, docs, and similar feature patterns to make tasks consistent with the repo.
   - Do not ask for information that can be discovered from the repo.

3. Generate the task list.
   - Write `tasks.md` beside the source `prd.md`.
   - Preserve useful existing `tasks.md` content when updating unless the user asks for regeneration.
   - Stop after writing the task list; do not begin executing tasks unless the user makes a separate request.

## Task List Requirements

- Group tasks by implementation phase.
- Every task line must start unchecked with `- [ ]`.
- Keep tasks agent-sized: each task should be small enough for a coding agent to implement and verify in one focused pass.
- Include implementation and verification tasks.
- Add docs, migration, rollout, or cleanup tasks only when supported by the PRD, design doc, or repo context.
- Avoid owners, due dates, or checked tasks unless the user explicitly asks.
- Do not include a broad product or design interview workflow.

## Suggested Structure

Use concise section headings that fit the feature. A typical `tasks.md` can look like:

```markdown
# <Feature Name> Tasks

## Setup and Discovery
- [ ] Confirm existing patterns for <area>.

## Implementation
- [ ] Implement <specific behavior>.

## Verification
- [ ] Add or update tests for <specific behavior>.
- [ ] Run the relevant checks and record results.
```

## Writing Guidelines

- Derive task order from dependencies in the PRD, design doc, and project structure.
- Prefer concrete file or subsystem references when they help implementation.
- Split tasks that mix unrelated code paths, risk areas, or verification steps.
- Keep task text action-oriented and directly implementable.
- Record assumptions or gaps as unchecked tasks only when they require later implementation work; otherwise put them in a short notes section.

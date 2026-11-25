---
name: tiger-docs
description: Resolve project decisions, write target behavior to docs/, and capture scoped acceptance criteria in a development specification.
disable-model-invocation: true
---

# Tiger Docs

Throughout this skill, write only to `docs/` and each current work item's `spec.md`. When migrating an existing documentation index from the root `AGENTS.md`, edit only the corresponding index content there. Treat all other files, including product code, tests, configuration and `tasks.md`, as read-only.

Record implementation gaps and observable acceptance criteria in the relevant `spec.md`, even when the fix is obvious or takes only one line. Leave implementation for a later stage.

Read the [Tiger documentation layout](../_shared/tiger-docs-layout.md) before locating or updating project documents.

## Workflow

1. Read applicable project instructions, existing docs, related code and any explicitly continued work item. Establish current behavior through inspection. Resolve material ambiguities one decision at a time, recommending an answer.
2. Group changes by delivery goal. Split specifications only when each outcome is useful independently, can ship if the other is postponed or cancelled, and can pass its own acceptance checks independently. Frontend, backend and tests for one login capability belong together. Ask when joint delivery is unclear.
3. Once decisions are settled, directly update the relevant topic sections in the project's documentation language. Replace superseded rules, maintain terminology and repair affected references. Update the documentation index according to the shared layout. Migrate an existing root AGENTS.md file-by-file index there. Keep a general description and index link in the root instructions, preserving unrelated guidance.
4. Write a concise `dev-docs/<number>-<kebab-case-description>/spec.md` for each work item. Record the goal, scope, actual gaps and observable acceptance criteria with relative links to the authoritative sections. Continue an existing spec only when explicitly identified; otherwise allocate the next global number after the highest existing directory, starting at 0001 and using at least four digits. Recheck paths before writing. Preserve sibling files.
5. Verify requirement coverage, the index and affected links. Report saved paths and remaining gaps. Ask whether to continue with task decomposition, including for documentation-only changes. If several specifications were produced, list their IDs and goals in that prompt. A selection confirms continuation; if the user agrees without selecting one, ask which specification to use. Stop if the user declines. Once confirmed for a single specification, read the full [Tiger Tasks instructions](../tiger-tasks/SKILL.md) and follow them from the first step in this session using the selected spec.md. If the instructions cannot be read, report the missing or unreadable path and stop.

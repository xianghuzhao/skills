---
name: tiger-implement
description: Implement Tiger docs/NNNN-name/tasks.md checklists, requiring all task items to start unchecked.
disable-model-invocation: true
---

# Tiger Implement

Use this skill to implement a task checklist created by `tiger-tasks`. The selected `tasks.md` must start with all Markdown task items unchecked.

## Workflow

1. Find the task file.
   - Use a user-provided `tasks.md` path or `docs/NNNN-name/` path when present.
   - Otherwise search `docs/NNNN-*/tasks.md` and choose the file in the highest numeric `NNNN` folder.
   - If no `tasks.md` exists, or if the highest numeric folder cannot be determined, ask one concise clarifying question and include a recommended choice.

2. Validate the starting checklist state before implementation.
   - Read the full `tasks.md`.
   - Treat Markdown task lines checked with `[x]` or `[X]` as violations.
   - Ignore headings, notes, prose, and non-task lines.
   - If any checked task exists, do not implement anything yet.
   - Ask the user whether to stop, reset all checkboxes to unchecked, or continue only with currently unchecked tasks.

3. Read planning context and inspect the project.
   - Read sibling `prd.md` and `design.md` when present.
   - Inspect manifests, config files, source layout, tests, docs, and similar implementations before editing.
   - Do not ask for information that can be discovered from the repo.

4. Implement the checklist.
   - Work through all unchecked tasks in dependency order unless blocked by failures, missing information, or unsafe ambiguity.
   - Keep edits scoped to the task list and the project areas implied by the PRD, design doc, and tasks.
   - Preserve unrelated user changes and do not revert work outside the implementation scope.

5. Update task state as work is verified.
   - Mark a task checked only after the intended change is implemented and the most relevant available verification for that task has run.
   - Leave failed, blocked, skipped, or unverified tasks unchecked.
   - If a task cannot be verified because no relevant check exists, leave it unchecked unless the task itself defines an acceptable manual verification.

6. Report the result.
   - Summarize completed work, remaining unchecked tasks, and any blockers.
   - Include the verification commands run and their results.
   - Mention any task intentionally left unchecked and why.

## Implementation Guidelines

- Prefer existing project patterns, frameworks, helper APIs, and tests.
- Run focused checks as tasks are completed, then run broader relevant verification before finishing when feasible.
- If implementation changes reveal that the checklist is stale or internally contradictory, stop and ask the user how to update the plan.
- Do not silently skip tasks. Either implement and verify them, leave them unchecked with a clear reason, or ask when the right action is ambiguous.

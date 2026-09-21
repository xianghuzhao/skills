---
name: tiger-tasks
description: Turn a selected change specification in dev-docs into independently verifiable implementation tasks, or incrementally maintain its existing tasks.md while preserving task IDs and progress. Use only when explicitly invoked.
disable-model-invocation: true
---

# Tiger Tasks

Translate a change specification produced by tiger-docs into implementation tasks that can be executed, verified, and tracked individually.

Documentation-only scope: create or update only `dev-docs/<number>-<kebab-case-description>/tasks.md` beside the selected `spec.md` in the target project. Read project rules, documentation, and code as needed. Do not modify the specification, project rules, product code, tests, configuration, or other files. Do not execute implementation tasks, mark tasks complete, or invoke another skill. Report the documentation result and stop.

## Questioning and approval

- Ask one question requiring one decision at a time, provide a recommended answer, and wait for the response before continuing.
- Discover repository facts through inspection instead of asking the user.
- Resolve all blockers before presenting the complete task proposal. Obtain the user's approval of that proposal before creating or updating `tasks.md`.
- If the user requests changes to the proposal, resolve them and present the complete revised proposal for approval before writing.

## Workflow

1. Establish the work item and current state.
   - Read the target project's root `AGENTS.md` and applicable local instructions.
   - Select the specification identified by the user's request or explicit session context. If the target is ambiguous, ask; never assume the highest-numbered directory is the intended work item. If the specification is missing, ask the user to provide or identify it instead of inventing requirements or creating a specification.
   - Read the selected `spec.md` in full, its authoritative documentation references, relevant implementation code, and any existing `tasks.md`.
   - Follow the documentation index and focused searches to find other current rules affecting the change. Establish the actual implementation gap and preserve unrelated workspace changes.

2. Resolve decisions that affect decomposition.
   - Use established project conventions and confirmed constraints. Ask about unresolved technical choices that change task boundaries, dependencies, or acceptance criteria; leave routine local coding choices to the implementation stage.
   - Treat `docs/` as the current source of truth and the specification as the requirements of its particular change. Clarify conflicts with current rules instead of silently following stale requirements or changing scope through tasks.
   - If resolution requires a specification or authoritative-document change, explain the required correction and wait for the user to have it resolved outside this skill. Re-read the corrected sources before continuing; do not modify them yourself.

3. Prepare an executable decomposition without writing.
   - Group work into independently verifiable implementation units. A task may span multiple files or layers when they form one coherent capability. Necessary foundations may be separate tasks when they have their own completion criteria.
   - Order tasks by their dependencies. Identify prerequisites by task ID and ensure there are no circular or missing dependencies.
   - Cover every applicable specification requirement and acceptance criterion. Include migration, compatibility, and integration verification work when the specification requires it.
   - Inspect existing behavior before adding work. If a requirement is already implemented, explain the evidence in the coverage notes rather than creating a completed task or unnecessary implementation work. Include verification work when evidence is insufficient.
   - Add only technical detail needed to execute and verify each task. Refer to authoritative requirements instead of duplicating the specification or prescribing every function and coding step.
   - For existing task documents, apply the maintenance rules below.

4. Present the complete proposal for approval.
   - Show the exact target path, all proposed tasks with their implementation guidance, dependencies, verification methods, and completion criteria, and how they cover the specification.
   - For updates, distinguish retained, changed, added, removed, and replaced tasks. Explain the reason for every removal or replacement and obtain approval for those changes as part of the complete proposal.
   - Ask for approval only after all prerequisite decisions and blockers are resolved. Do not write a partial document while waiting for answers.

5. Write and verify the approved task document.
   - Recheck the selected specification and existing task document before writing. If intervening changes affect the approved proposal, reconcile them and obtain approval for the revised proposal rather than overwriting them.
   - Use the project's documentation language and style, preserving useful structure and unrelated content in an existing task document.
   - Write only the approved `tasks.md`. Verify requirement coverage, task IDs, dependencies, completion states, and relative links against the source documents.
   - Report the path, task changes, dependency order, and verification performed on the document. Clearly distinguish document validation from implementation verification, then stop.

## Task document conventions

- Begin with a relative link to `spec.md` and a short explanation of the implementation goal. Record confirmed implementation choices and their rationale only when needed to understand the tasks.
- Give each task a stable ID and completion checkbox, for example `- [ ] T001: Implement refresh-token rotation`. New tasks start unchecked. Allocate new IDs after the highest existing ID, preserving an existing ID convention when present; never renumber retained tasks or reuse removed IDs within an update.
- Each task must state its goal, corresponding specification requirements, affected modules or code areas, necessary implementation details, prerequisite task IDs (or none), verification method, and observable completion criteria.
- Use repository-specific paths and verification commands when established by inspection. Do not invent file paths or commands as existing facts; identify proposed additions as new.
- Include concise coverage notes mapping specification requirements and acceptance criteria to task IDs or evidence of existing implementation. Do not copy the full specification into the task document.
- Verification instructions belong to future task execution. Writing or reviewing the task document does not establish that those checks pass or that any implementation task is complete.

## Maintaining existing tasks

- Preserve still-applicable task IDs, completion states, and useful implementation notes. Update affected tasks in place rather than regenerating the entire document.
- Never check or uncheck an existing task on your own. If an existing completion claim appears inconsistent with the code, explain the discrepancy and clarify the necessary follow-up with the user.
- When completed work needs rework, retain its completion record and add a new unchecked follow-up task with an explicit reference to the original task. Do not rewrite the completed task as though it had always required the new behavior.
- Explain obsolete tasks and proposed removals or replacements during review. Preserve completed-task history and repair dependencies and coverage references when approved changes retire or replace tasks.
- Do not expand the work item to include unrelated cleanup discovered during inspection.

---
name: tiger-implement
description: Execute code and documentation tasks after approval of a complete file-change proposal, recording verified completion and blockers in tasks.md.
disable-model-invocation: true
---

# Tiger Implement

Implement the remaining code and documentation work in a selected `dev-docs/<number>-<kebab-case-description>/tasks.md`, including documentation-only work items. Obtain approval of the complete implementation proposal before changing files, then execute tasks in dependency order and record verified progress.

Implementation scope comes from the selected task document and its specification. Maintain only progress and verification records in `tasks.md`; do not redefine tasks or requirements. Do not automatically invoke tiger-docs or tiger-tasks. Implementation approval does not itself authorize commits, pushes, or deployment.

## Questioning and approval

- Discover repository facts through inspection instead of asking the user.
- Ask one question requiring one decision at a time, recommend an answer, and wait for the response before continuing.
- Resolve ambiguities affecting the overall proposal before presenting it for approval. Approval of the specification or task decomposition is not approval of the implementation file-change proposal.
- Before implementation approval, inspect and prepare without changing files, including `tasks.md`. If the user requests revisions, present the complete revised proposal and obtain approval before writing.

## Workflow

1. Establish the work item and current state.
   - Read the target project's root `AGENTS.md` and applicable local instructions.
   - Select the task document identified by the user's request or explicit session context. If missing or ambiguous, ask the user to identify it; never assume the highest-numbered directory is intended or invent a replacement task document.
   - Read the complete `tasks.md`, its sibling `spec.md`, authoritative documentation references, and relevant code. Ask for missing required sources instead of inventing requirements.
   - Follow the documentation index and focused searches for current rules. Apply replacements explicitly approved in the specification within this work item's scope; other project rules remain applicable. Clarify unexplained contradictions or conflicts caused by later changes rather than treating an approved replacement as a new conflict.
   - Inspect existing workspace changes and preserve unrelated work. Identify all unfinished tasks, prerequisites, verification methods, and observable completion criteria. Check for missing or circular dependencies.
   - Check that tasks documenting new behavior depend on the corresponding implementation passing verification. Treat missing dependencies as task-definition issues to resolve before presenting the proposal.
   - Preserve existing completed-task history. If a completion claim conflicts with the code, report the discrepancy rather than unchecking the task or silently redefining its scope.

2. Prepare and present the complete implementation proposal.
   - Cover all unfinished tasks in the selected document and establish their dependency order for execution. If none remain, report that fact without inventing work.
   - Resolve uncertainties that prevent a complete proposal. If requirements or task definitions need correction, explain the required correction and wait for the user or tiger-tasks to resolve it outside this skill; re-read corrected sources before proceeding.
   - Present the complete proposal using the format below. Show all required code and documentation changes, including affected tests, configuration, dependency manifests and lockfiles, migrations, generated files, and `AGENTS.md` index or convention updates. Exclude only the task progress document `tasks.md` from the displayed file lists, and do not reproduce the task list. For moves, identify both source and destination paths and affected reference repairs.
   - Keep the relationship between file changes and tasks clear during preparation so all unfinished tasks are covered. Excluding `tasks.md` from the display does not remove the progress updates required in step 4; those updates are covered by implementation approval.
   - Explain verification and known environment constraints. Display every affected category before asking once for explicit approval of the whole proposal; do not pause for category-by-category approval.

3. Implement the approved work.
   - Recheck relevant sources and workspace state before editing so intervening changes are not overwritten. Resolve changed requirements or invalidated assumptions before continuing affected work.
   - Execute tasks in dependency order, using project conventions and applicable implementation guidance. Approval covers the whole proposal; do not request approval again for each task.
   - Update documentation describing new behavior only after its corresponding implementation passes verification. If that implementation is blocked or unverified, leave dependent documentation unchanged and continue independent work. Documentation-only changes may execute independently.
   - Necessary file additions, edits, or deletions beyond the initial list are allowed when required to complete the original tasks. Track the differences and their reasons for the final report; routine file-scope adjustments do not require renewed approval.
   - This flexibility does not authorize new requirements, unrelated cleanup, or changes to task definitions, dependencies, or completion criteria. Treat such needs as blockers for affected work rather than expanding the assignment.
   - Try to resolve implementation and verification failures within the approved scope. When a blocker cannot be resolved independently, record it, skip tasks that depend on the blocked work, and continue independent tasks. Preserve partial work and explain its state.
   - If all remaining tasks are blocked, report what is needed to continue rather than repeatedly retrying or claiming completion.

4. Verify and update progress as work completes.
   - Check a task only after its implementation meets the documented completion criteria and its required verification passes. Update that task promptly rather than waiting until the entire document is finished.
   - For documentation tasks, verify the required content, replacement or removal of superseded rules, and affected links and section references. Check planned index and convention updates against the final document paths while preserving unrelated project instructions.
   - Add concise verification results beneath the task, using established document language and structure. Record commands and outcomes or other evidence appropriate to its verification method.
   - Leave tasks unchecked when verification fails or cannot run, even if code has been written. Record the failure, missing prerequisite, or environment constraint and what remains to be done.
   - Update existing progress notes in place to keep the current state clear. Preserve useful history and unrelated content; do not renumber tasks, change their definitions or dependencies, add replacement tasks, or rewrite completion criteria to fit the implementation.
   - Run applicable integration checks after dependent work is assembled. If new evidence invalidates a task checked during this run, correct that new completion claim and record the failure. Do not silently alter completion records that predate this run; report discrepancies for resolution.

5. Report the implementation result and stop.
   - Summarize completed task IDs, remaining tasks, blockers, and partial implementation where relevant.
   - Report verification performed and distinguish passing checks, failures, and checks that could not run.
   - Summarize actual file changes, identifying additions, edits, or deletions that differed from the approved proposal and explaining why they were necessary.
   - Link the updated `tasks.md` and state any input or environment changes needed to finish remaining work.

## Implementation proposal format

Use the following structure, translating headings and explanations into the user's communication language. Repeat the category block for each affected category, then include overall verification and a single approval request.

````markdown
## Implementation proposal: <change name>

Goal: <observable functionality or behavior after implementation>

### <Category, such as Backend>

#### Modified and deleted files

| Action | File path | Change description |
|---|---|---|
| Modify | <path> | <what changes and why> |
| Delete | <path> | <why it is removed and what happens to its responsibilities> |

#### New files

```text
<directory>/
|-- <new file>
`-- <subdirectory>/
    `-- <new file>
```

| File path | Responsibility and new contents |
|---|---|
| <full repository-relative path> | <brief explanation> |

#### Verification

<category-specific checks, passing criteria, and known environment constraints>

<!-- Repeat the category block for all other affected categories. -->

### Overall verification

<cross-category integration checks and passing criteria, or None if not applicable>

### Approval

This is the complete proposal for all affected categories. Implementation starts
after approval. File-scope adjustments necessary to complete the original tasks
may be made during implementation and will be explained in the final report.
New requirements and task-definition changes are outside this authorization.

Do you approve this complete implementation proposal?
````

### Filling the template

- Choose categories from the actual project, such as Backend, Frontend, Database migrations, Documentation, and Shared and engineering configuration. Omit unaffected categories; documentation-only proposals need no code categories.
- Group each area's tests, configuration, and dependency files with that area. Put database migrations in their own category and cross-module shared files in Shared and engineering configuration.
- List each file once; explain cross-category effects in its description. Category order is for presentation only; execution still follows task dependencies.
- Use repository-relative paths and enumerate individual files. Do not substitute directory names, wildcards, or phrases such as "and other files" for the file list.
- Keep each file description to one or two concrete sentences. For deletions, explain whether responsibilities move elsewhere or are removed.
- Show only new files and their necessary parent directories in each tree. The table following the tree must describe every new file using its full repository-relative path.
- State None for any absent action type: modifications, deletions, or additions. Omit empty tables and trees while retaining the relevant subsection.
- Describe planned verification without claiming it has passed. Resolve ambiguities that prevent a complete proposal before requesting approval.
- Replace all placeholders and omit the template's instructional comment in the actual proposal. If revisions are requested, display the complete revised proposal, not only the changed category.

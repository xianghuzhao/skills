---
name: tiger-docs
description: Resolve project decisions, write target behavior to docs/, and capture scoped acceptance criteria in a development specification.
disable-model-invocation: true
---

# Tiger Docs

## Directory roles

- `docs/` holds long-lived project documentation. Write confirmed target behavior and rules in their authoritative topic pages, even when implementation is pending. Maintain the documentation index in `docs/README.md`.
- `dev-docs/` holds work-item material. Its `spec.md` captures scope, implementation gaps and acceptance criteria; its sibling `tasks.md` tracks execution. Prefer keeping this directory local and out of Git unless the project's collaboration conventions call for sharing it. Leave ignore configuration to the project. Link from specifications to formal docs and keep formal docs usable without local work items.

## Workflow

1. Read applicable project instructions, existing docs, related code and any explicitly continued work item. Establish current behavior through inspection. Resolve material ambiguities one decision at a time, recommending an answer.
2. Group changes by delivery goal. Split specifications only when each outcome is useful independently, can ship if the other is postponed or cancelled, and can pass its own acceptance checks independently. Frontend, backend and tests for one login capability belong together. Ask when joint delivery is unclear.
3. Once decisions are settled, directly update the relevant topic sections in the project's documentation language. Replace superseded rules, maintain terminology and repair affected references. Maintain a sorted index of maintained docs with relative links and short descriptions in docs/README.md, omitting the index itself and generated or binary assets. Migrate an existing root AGENTS.md file-by-file index there. Keep a general description and index link in the root instructions, preserving unrelated guidance.
4. Write a concise `dev-docs/<number>-<kebab-case-description>/spec.md` for each work item. Record the goal, scope, actual gaps and observable acceptance criteria with relative links to the authoritative sections. Continue an existing spec only when explicitly identified; otherwise allocate the next global number after the highest existing directory, starting at 0001 and using at least four digits. Recheck paths before writing. Preserve sibling files.
5. Verify requirement coverage, the index and affected links. Report saved paths and remaining gaps. Ask whether to continue with tiger-tasks even for documentation-only changes. On confirmation, use the selected spec as input; if several were produced, list their IDs and goals and ask which one to continue. Wait for the user's choice before starting that stage.

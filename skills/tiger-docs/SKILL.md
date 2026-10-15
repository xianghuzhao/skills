---
name: tiger-docs
description: Resolve project decisions and write approved change specifications covering implementation requirements and documentation modification plans.
disable-model-invocation: true
---

# Tiger Docs

Turn confirmed decisions into change specifications for later execution. Read the project's code, documentation, and rules to establish the gap between the current state and the agreed target.

Write only `dev-docs/<number>-<kebab-case-description>/spec.md` in the target project. Treat `docs/`, root `AGENTS.md`, and implementation files as read-only inputs; describe their required changes in the specification. Preserve other files, including `tasks.md`. Do not create implementation tasks, placeholder files, or a specification index, or automatically invoke another skill.

## Questioning and approval

- Ask one unresolved decision at a time, provide a recommended answer, and wait for the response. Resolve prerequisites before dependent decisions.
- Ask only about material ambiguities in goals, scope, behavior, constraints, acceptance criteria, or documentation meaning. Reuse explicit instructions and established project conventions; handle wording and routine editorial choices independently.
- Discover repository facts through inspection. Finish that exploration before asking questions that depend on it.
- Obtain approval of the complete specification drafts and exact target paths before writing. This is the single approval for this skill; shared understanding and document structure need no separate approval rounds. If revisions are requested, present the complete revised drafts for approval.

## Workflow

1. Establish the current state.
   - Read the repository's `AGENTS.md` and applicable local instructions. Use the documentation index, headings, and focused searches to locate affected documentation, then read it in full. Expand the search when the index is absent, stale, or insufficient.
   - Inspect related code to verify current behavior. Distinguish existing workspace changes from the changes requested in this session.
   - Inspect numbered directories under `dev-docs/`; read the existing specification when the user explicitly continues a work item.
   - Complete when the affected sources and current behavior are established, with uncertainties identified for resolution.

2. Resolve the target.
   - Clarify material decisions using the questioning rules. For replacements, establish which existing rules change and what replaces them; for unexplained contradictions, clarify whether to retain, replace, or scope the rules so they coexist.
   - Apply approved replacements within this work item's scope; other project rules remain applicable. Clarify conflicts caused by later changes or differences the specification does not explain. Record unrelated pre-existing conflicts for the report without expanding the work item.
   - Complete when every material decision needed to define the change and its acceptance criteria is resolved. If implementation, documentation, and relevant project conventions already satisfy the target, report the evidence and stop without creating a specification.

3. Prepare complete specifications without writing.
   - Capture confirmed decisions and necessary rationale, scope, constraints, consequences, and verifiable acceptance criteria. Record requirements rather than the interview transcript or coding steps.
   - Cover every implementation and documentation gap introduced, changed, or removed by this session. Include documentation-only changes even when no code changes are needed.
   - Map each decision to every affected document and prepare its modification plan using the conventions below. Resolve uncertainties affecting scope or requirements before presenting drafts.
   - Group changes with a shared goal and dependencies into one specification; split independently implementable and verifiable changes into separate work items. Allocate paths using the numbering conventions below.
   - Complete when every confirmed change is covered by a draft and its acceptance criteria, with existing facts distinguished from proposed changes.

4. Present the complete drafts for approval.
   - Show every full target specification path and its complete draft, identifying new work items and updates to explicitly continued items. Include all proposed documentation changes within the drafts.
   - Complete when the user has approved the full set of drafts and paths under the questioning and approval rules.

5. Write, verify, and report.
   - Recheck relevant sources, existing specifications, numbering, and target paths. If intervening changes invalidate the approved content or occupy a new path, reconcile them and obtain approval of the complete revised drafts and paths before writing.
   - Write only the approved specifications. Verify coverage of confirmed requirements, agreement between implementation requirements and documentation plans, and relative links to existing sources. Clearly identify planned paths and sections as future targets rather than existing references.
   - Complete when the saved specifications match the approved drafts and these checks pass. Report their paths, implementation impact, planned documentation changes, and verification results. Explain any unrelated pre-existing conflicts, then stop.

## Specification conventions

- Use `dev-docs/<number>-<kebab-case-description>/spec.md`, for example `dev-docs/0001-add-new-button/spec.md`. Use a short English description and allocate globally from the highest existing numbered directory plus one, starting at `0001`, with at least four digits. Allocate successive numbers for multiple work items; preserve existing numbers and gaps.
- Default to a new directory. Update an existing specification only when the user explicitly continues that work item; ask if the target is ambiguous. Integrate the new decisions while preserving still-applicable requirements and other files in that directory. Independent later changes leave old specifications intact.
- Follow the project's documentation language and style. Explain background and goals, scope and exclusions, current and target behavior, necessary constraints, verifiable acceptance criteria, and the documentation modification plan. Cover interfaces, data, migrations, and compatibility only when relevant.
- Link to existing source documents and sections using relative links. Identify the old rules being replaced and their approved replacements so later execution can distinguish intentional changes from unexplained conflicts. Include the requirements needed to implement the change without copying entire project documents.

## Documentation modification plans

- For every affected document, identify the file and section, rules to add or replace, obsolete conclusions to remove, and necessary link or section-reference changes. Specify content requirements; leave final prose to execution. State explicitly when no documentation changes are needed.
- Prefer existing categories, names, and ownership. Give each subject one authoritative home and plan necessary summaries or links elsewhere. Integrate changes in their semantic locations, replacing superseded statements; retain history only for migration, compatibility, or active constraints.
- For new documents, splits, or moves, list full target paths, responsibilities, source paths and sections, content to move, and affected incoming and outgoing links. Limit reorganization to documents affected by this change. Capture required reference repairs outside `docs/` as explicit requirements so the plan leaves no broken references.
- Propose nested directories when subject, module, or domain relationships improve ownership or navigation; explain each new directory's purpose. Use existing naming conventions, otherwise kebab-case. Choose depth from content relationships, with no fixed depth or file-count threshold; propose only directories needed by actual documents.
- When terminology changes, plan updates to the existing glossary and affected usages. Propose a glossary only when none exists and the change needs project-specific or easily confused terms defined. Use one preferred term per concept, with useful aliases.
- When document changes affect the root `AGENTS.md` documentation index, plan an update to its single `## Documentation Index` section. The resulting index should list every maintainable source under `docs/`, recursively and sorted by relative path, with relative links and one-sentence descriptions; exclude images, attachments, and generated artifacts. Use final paths after moves.
- When missing or stale, plan a concise root `AGENTS.md` convention for `dev-docs/<number>-<kebab-case-description>/spec.md`, its purpose and naming, and how approved replacements relate to other current project rules. Preserve unrelated instructions and keep individual specifications out of the index.
- Tie documentation acceptance criteria to the confirmed target and reference integrity. Documentation describing new behavior is executed only after its corresponding implementation passes verification; documentation-only changes may execute independently.

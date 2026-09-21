---
name: tiger-docs
description: Resolve project decisions, integrate confirmed outcomes into engineering docs and the AGENTS.md documentation index, and write numbered change specifications when those outcomes require code changes. Use only when explicitly invoked.
disable-model-invocation: true
---

# Tiger Docs

Maintain internal engineering documentation as the project's current source of truth. Resolve the decisions in the current session, integrate the confirmed results into the existing documentation structure, and capture required implementation changes in specifications.

Documentation-only scope: modify maintainable documentation under `docs/`, change specifications at `dev-docs/<number>-<kebab-case-description>/spec.md`, and the documentation index and development-document conventions in the root `AGENTS.md` only. These paths belong to the target project. Read related code as needed, but do not change product code, tests, configuration, migrations, source comments, or generated files. Do not create implementation tasks, placeholder files, or a `dev-docs/README.md`, or invoke another skill. Stop after reporting the documentation result.

## Questioning rules

- Throughout the workflow, ask only one question requiring one decision at a time, provide a recommended answer, and wait for the user's response before asking the next question. This applies to decision clarification, conflict resolution, terminology choices, and approvals.
- Do not bundle independent decisions into one question.

## Workflow

1. Ground in the project.
   - Find the repository root and read its `AGENTS.md` when present.
   - Recursively inventory maintainable documentation sources under `docs/`, including formats such as Markdown, AsciiDoc, reStructuredText, text, and maintained API specifications. Exclude images, attachments, and generated artifacts.
   - Use the documentation index, document headings, and focused searches to identify relevant documents, then read those documents in full. Expand the read when the index is absent, stale, or insufficient to detect affected content or conflicts.
   - Establish the starting state of affected documents and distinguish existing workspace changes from this session's confirmed changes. Inspect existing numbered directories under `dev-docs/` and read an existing specification when the user explicitly continues that work item.
   - Discover repository facts instead of asking the user. An unfinished exploration is an unresolved prerequisite, not a question for the user.

2. Resolve decisions as a design tree.
   - Model each decision as a node whose dependent decisions branch from it.
   - Select one unresolved decision whose prerequisites are settled and ask about it.
   - Wait for the user's answer, update the decision tree, and select the next question based on the updated tree.
   - Continue until every relevant branch is resolved and no decision is silently assumed.
   - Do not prepare or modify documentation until the user explicitly confirms that shared understanding has been reached.

3. Prepare the integration without writing.
   - Extract confirmed decisions and the rationale, constraints, scope, and consequences necessary to understand them. Do not preserve the interview transcript.
   - Map each decision to every existing document whose meaning it affects. Common categories include product, architecture, data model, glossary, coding standards, API, testing, and release, but use other coherent categories when the subject does not fit them.
   - Prefer the project's existing document structure, categories, and names. Without an applicable convention, use `docs/<kebab-case-category>.md`.
   - Give each subject one authoritative document. Plan only the necessary summaries, constraints, or links in other affected documents.
   - Identify implementation requirements introduced, changed, or removed by this session's confirmed documentation changes. Do not treat the entire workspace diff as this session's scope.
   - Read related code as needed to establish current behavior and the gap to the confirmed target. Generate specifications only when implementation changes are needed; skip formatting, wording, and documentation of behavior already implemented. Mark unverified facts explicitly and resolve uncertainty that affects the scope, requirements, or decision to generate before writing.
   - Group changes that share a goal and depend on one another into one specification, even when they affect multiple documents. Split independently implementable and verifiable changes into separate work items. Choose their paths using the change specification conventions below.

4. Resolve all blockers before changing files.
   - Treat contradictory outcomes, overlapping rules with different effects, and apparent replacements of existing decisions as conflicts.
   - For each conflict affecting this integration, ask the user whether to retain the existing decision, adopt the new decision, or scope both decisions so they can coexist. Do not choose silently.
   - Resolve unclear target categories, scope boundaries, and the meaning or preferred name of affected terms with the user.
   - Identify needed new documents and splits. Propose a split when unrelated categories obscure a document's ownership or navigation. Propose a glossary only if none exists and the confirmed decisions introduce or clarify project-specific or easily confused terms.
   - For new documents, splits, or specifications, settle the independent decisions, then present one complete information-architecture proposal containing every target path, each document's responsibility, content to move, and resulting links. Include specification grouping and whether each specification is new or updates an explicitly continued work item. Ask for approval of the whole proposal before writing; this approval also covers the specifications without a separate approval round. If changes are requested, resolve them and resubmit the complete proposal.
   - Finish the entire preflight before writing. If any blocking issue remains unresolved, make no documentation changes.
   - Do not repair unrelated pre-existing conflicts. Record them for the final report; promote one to a blocker only if it affects the current integration.

5. Integrate the confirmed decisions.
   - Preserve each document's purpose, useful content, structure, style, terminology, and language.
   - Rewrite or merge relevant passages in their semantically correct locations. Never mechanically append new decisions to the end of a document.
   - Keep the complete explanation in the authoritative document and update other affected documents only as needed to remain consistent and navigable.
   - When a confirmed decision replaces an old one, rewrite or remove superseded statements so the documentation expresses the current truth. Retain brief history only when migration, compatibility, or an active constraint requires it.

6. Maintain terminology when needed.
   - Update the existing glossary while preserving its structure, or create one if approved in preflight. Place affected project-specific terms in the appropriate categories and positions.
   - Use one preferred term per concept and record alternate names as aliases when useful.

7. Write the approved change specifications.
   - Use the confirmed requirements, integrated documentation, and verified implementation gap to write each approved `spec.md` according to the conventions below.
   - Check that each specification agrees with the updated authoritative documents and that its relative links and section references resolve.

8. Refresh the documentation index and directory conventions.
   - Create the root `AGENTS.md` if it does not exist; otherwise preserve every unrelated instruction.
   - Add or update exactly one `## Documentation Index` section after documentation integration is complete.
   - Recursively list every maintainable documentation source under `docs/`, sorted by relative path. Each entry must contain a relative Markdown link and a one-sentence description derived from that document's content.
   - Do not list images, attachments, or generated artifacts as documentation entries.
   - Add or update a concise development-document convention in the root `AGENTS.md`: explain the purpose and naming of `dev-docs/<number>-<kebab-case-description>/spec.md`, its relationship to current rules in `docs/`, and the need to clarify conflicts before implementing an old specification. Preserve unrelated instructions and avoid duplicating an existing convention.
   - Do not enumerate specifications in `AGENTS.md` or create a separate specification index. Directory names and the final report locate individual specifications.

9. Report and stop.
   - Summarize the decisions integrated, documents created or updated, glossary changes, and index changes.
   - Report unrelated pre-existing conflicts without changing them.
   - List created or updated specification paths, their implementation impact, and authoritative documents that a later explicit coding request should follow. If no specification was generated or updated, explain why.

## Change specification conventions

- Use `dev-docs/<number>-<kebab-case-description>/spec.md`, for example `dev-docs/0001-add-new-button/spec.md`. Use a short English description. Allocate numbers globally from the highest existing numbered directory plus one, starting at `0001`, with at least four digits. For multiple new work items, allocate successive numbers. Do not fill gaps or renumber existing directories.
- Default to a new directory. Update an existing specification only when the user explicitly continues that work item; ask if the target is ambiguous. Preserve other files in that directory, including any future `tasks.md`.
- Recheck numbering and target paths before writing. Never overwrite an existing directory to create a new work item. If the approved new number or path has become occupied, resolve the collision and confirm the revised path before writing.
- Follow the project's documentation language and style. Each specification must explain the complete change through background and goals, scope and exclusions, current and target behavior, necessary constraints, verifiable acceptance criteria, and relative links to authoritative `docs/` sections. Cover interface, data, migration, or compatibility changes only when relevant.
- Include the rules necessary to implement this change without copying whole project documents. Describe observable requirements and confirmed constraints, not coding steps, implementation ordering, or task checklists.
- `docs/` remains the current source of truth; a specification records the requirements of its particular change. Independent later changes do not rewrite old specifications. When explicitly continuing a work item, integrate the new decisions into its specification while preserving still-applicable requirements. Clarify conflicts with current rules before implementing an old specification; do not silently follow stale requirements.

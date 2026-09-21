---
name: tiger-docs
description: Maintain internal engineering documentation by resolving project decisions and coherently integrating confirmed outcomes into categorized docs and the AGENTS.md documentation index. Use only when explicitly invoked.
disable-model-invocation: true
---

# Tiger Docs

Maintain internal engineering documentation as the project's current source of truth. Resolve the decisions in the current session, then integrate the confirmed results into the existing documentation structure.

Documentation-only scope: modify maintainable documentation under `docs/` and the documentation index in the root `AGENTS.md` only. Do not change product code, tests, configuration, migrations, source comments, or generated files. Do not create implementation tasks or invoke another skill. Stop after reporting the documentation result.

## Questioning rules

- Throughout the workflow, ask only one question requiring one decision at a time, provide a recommended answer, and wait for the user's response before asking the next question. This applies to decision clarification, conflict resolution, terminology choices, and approvals.
- Do not bundle independent decisions into one question.

## Workflow

1. Ground in the project.
   - Find the repository root and read its `AGENTS.md` when present.
   - Recursively inventory maintainable documentation sources under `docs/`, including formats such as Markdown, AsciiDoc, reStructuredText, text, and maintained API specifications. Exclude images, attachments, and generated artifacts.
   - Use the documentation index, document headings, and focused searches to identify relevant documents, then read those documents in full. Expand the read when the index is absent, stale, or insufficient to detect affected content or conflicts.
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

4. Resolve all blockers before changing files.
   - Treat contradictory outcomes, overlapping rules with different effects, and apparent replacements of existing decisions as conflicts.
   - For each conflict affecting this integration, ask the user whether to retain the existing decision, adopt the new decision, or scope both decisions so they can coexist. Do not choose silently.
   - Resolve unclear target categories, scope boundaries, and the meaning or preferred name of affected terms with the user.
   - Identify needed new documents and splits. Propose a split when unrelated categories obscure a document's ownership or navigation. Propose a glossary only if none exists and the confirmed decisions introduce or clarify project-specific or easily confused terms.
   - For new documents or splits, settle the independent decisions, then present one complete information-architecture proposal containing every target path, each document's responsibility, content to move, and resulting links. Ask for approval of the whole proposal before writing. If changes are requested, resolve them and resubmit the complete proposal.
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

7. Refresh the documentation index.
   - Create the root `AGENTS.md` if it does not exist; otherwise preserve every unrelated instruction.
   - Add or update exactly one `## Documentation Index` section after documentation integration is complete.
   - Recursively list every maintainable documentation source under `docs/`, sorted by relative path. Each entry must contain a relative Markdown link and a one-sentence description derived from that document's content.
   - Do not list images, attachments, or generated artifacts as documentation entries.

8. Report and stop.
   - Summarize the decisions integrated, documents created or updated, glossary changes, and index changes.
   - Report unrelated pre-existing conflicts without changing them.
   - When the confirmed documentation requires code changes, list the implementation impact and authoritative documents that a later explicit coding request should follow.

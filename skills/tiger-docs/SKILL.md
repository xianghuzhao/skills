---
name: tiger-docs
description: Maintain internal engineering documentation by resolving project decisions and coherently integrating confirmed outcomes into categorized docs and the AGENTS.md documentation index. Use only when explicitly invoked.
disable-model-invocation: true
---

# Tiger Docs

Maintain internal engineering documentation as the project's current source of truth. Resolve the decisions in the current session, then integrate the confirmed results into the existing documentation structure.

Documentation-only scope: modify maintainable documentation under `docs/` and the documentation index in the root `AGENTS.md` only. Do not change product code, tests, configuration, migrations, source comments, or generated files. Do not create implementation tasks or invoke another skill. Stop after reporting the documentation result.

## Workflow

1. Ground in the project.
   - Find the repository root and read its `AGENTS.md` when present.
   - Recursively inventory maintainable documentation sources under `docs/`, including formats such as Markdown, AsciiDoc, reStructuredText, text, and maintained API specifications. Exclude images, attachments, and generated artifacts.
   - Use the documentation index, document headings, and focused searches to identify relevant documents, then read those documents in full. Expand the read when the index is absent, stale, or insufficient to detect affected content or conflicts.
   - Discover repository facts instead of asking the user. Use parallel exploration or sub-agents when available; otherwise explore locally. An unfinished exploration is an unresolved prerequisite, not a question for the user.

2. Resolve decisions as a design tree.
   - Model each decision as a node whose dependent decisions branch from it.
   - In each round, compute the frontier: every unresolved decision whose prerequisites are settled. Ask the whole frontier in one round, number each question, and give a recommended answer for each.
   - Do not ask a question whose answer depends on another unresolved question in the same round. Wait for the user's answers, update the tree, and recompute the frontier.
   - Continue until the frontier is empty and no branch is silently assumed.
   - Do not prepare or modify documentation until the user explicitly confirms that shared understanding has been reached.

3. Prepare the integration without writing.
   - Extract confirmed decisions and the rationale, constraints, scope, and consequences necessary to understand them. Do not preserve the interview transcript.
   - Map each decision to every existing document whose meaning it affects. Common categories include product, architecture, data model, glossary, coding standards, API, testing, and release, but use other coherent categories when the subject does not fit them.
   - Prefer the project's existing document structure, categories, and names. Without an applicable convention, use `docs/<kebab-case-category>.md`.
   - Give each subject one authoritative document. Plan only the necessary summaries, constraints, or links in other affected documents.

4. Resolve all blockers before changing files.
   - Treat contradictory outcomes, overlapping rules with different effects, and apparent replacements of existing decisions as conflicts.
   - For each conflict affecting this integration, ask the user whether to retain the existing decision, adopt the new decision, or scope both decisions so they can coexist. Do not choose silently.
   - Also stop when the target category, scope boundary, or preferred term is unclear.
   - When a new document or split is warranted, present one complete information-architecture proposal containing every target path, each document's responsibility, content to move, and resulting links. Wait for approval before creating or splitting anything.
   - Finish the entire preflight before writing. If any blocking issue remains unresolved, make no documentation changes.
   - Do not repair unrelated pre-existing conflicts. Record them for the final report; promote one to a blocker only if it affects the current integration.

5. Integrate the confirmed decisions.
   - Preserve each document's purpose, useful content, structure, style, terminology, and language.
   - Rewrite or merge relevant passages in their semantically correct locations. Never mechanically append new decisions to the end of a document.
   - Keep the complete explanation in the authoritative document and update other affected documents only as needed to remain consistent and navigable.
   - When a confirmed decision replaces an old one, rewrite or remove superseded statements so the documentation expresses the current truth. Retain brief history only when migration, compatibility, or an active constraint requires it.
   - If a document mixes enough unrelated categories to obscure ownership or navigation, propose a split under the approval rule before changing it.

6. Maintain terminology when needed.
   - When a glossary exists, preserve its structure and integrate affected project-specific terms in the appropriate categories and positions.
   - Use one preferred term per concept and record alternate names as aliases when useful. Ask the user when the preferred meaning or term is unclear.
   - If no glossary exists, propose creating one only when the confirmed decisions introduce or clarify project-specific or easily confused terms. Include it in the information-architecture proposal and do not create it without approval.

7. Refresh the documentation index.
   - Create the root `AGENTS.md` if it does not exist; otherwise preserve every unrelated instruction.
   - Add or update exactly one `## Documentation Index` section after documentation integration is complete.
   - Recursively list every maintainable documentation source under `docs/`, sorted by relative path. Each entry must contain a relative Markdown link and a one-sentence description derived from that document's content.
   - Do not list images, attachments, or generated artifacts as documentation entries.

8. Report and stop.
   - Summarize the decisions integrated, documents created or updated, glossary changes, and index changes.
   - Report unrelated pre-existing conflicts without changing them.
   - When the confirmed documentation requires code changes, list the implementation impact and authoritative documents that a later explicit coding request should follow. Do not implement the changes, generate task documents, or refer the work to another skill.

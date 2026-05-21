---
name: tiger-design
description: Create technical design docs from PRDs by reading docs/NNNN-name/prd.md, inspecting the repo, and writing design.md beside the PRD.
---

# Tiger Design

Use this skill to create an implementation-ready technical design from a PRD created by `tiger-prd`. Default to writing `design.md` beside the source PRD, for example `docs/0001-name/design.md`.

Documentation-only scope: write or update the design document only. Do not implement the feature, change product code, add tests, install dependencies, run migrations, or modify non-design files as part of this skill.

## Workflow

1. Find the source PRD.
   - Use a user-provided PRD path when present.
   - Otherwise infer the PRD name from the chat and search for matching `docs/NNNN-*/prd.md` files.
   - If there is exactly one plausible match, use it.
   - If there are multiple plausible matches or none, ask one clarifying question and include a recommended choice.

2. Read the PRD and inspect the project before asking questions.
   - Read the full PRD first.
   - Inspect manifests, config files, source layout, tests, docs, and similar implementations to infer the existing stack and conventions.
   - Do not ask the user for information that can be discovered from the repo.

3. Grill the user one question at a time.
   - Ask exactly one material question per turn.
   - Provide a recommended answer with every question.
   - Resolve technical dependencies in order: architecture, stack fit, dependencies, data flow, APIs or interfaces, storage, project structure, file structure, compatibility, testing, rollout, and risks.
   - Keep asking until the design is implementation-ready.
   - If the user asks to proceed with ambiguity, state the assumptions and continue.

4. Write the design doc.
   - Write `design.md` in the same folder as the source `prd.md`.
   - Preserve existing useful `design.md` content when updating unless the user asks for a rewrite.
   - Stop after writing the design doc; do not proceed into tasks or implementation unless the user makes a separate request.

## Design Doc Requirements

Use section names and depth that fit the project and PRD. The design doc must include:

- Technical stack and how it matches the existing project.
- Dependencies to add, remove, or rely on, including why each is needed.
- Project structure and where the feature belongs.
- Architecture and data flow.
- File structure for new or changed files.
- Design decisions and tradeoffs.
- Testing approach.
- Risks, open questions, or assumptions.

## Writing Guidelines

- Make the design consistent with the existing repo instead of inventing a parallel architecture.
- Prefer existing frameworks, libraries, conventions, and helper APIs.
- Be concrete enough that an implementer can start without making major technical decisions.
- Keep the document technical, but avoid writing code unless a short signature or interface sketch removes ambiguity.
- Separate confirmed decisions from assumptions and open questions.
- Mention migrations, compatibility, rollout, or observability only when the PRD or repo context makes them relevant.

---
name: tiger-prd
description: Create lean PRDs from feature ideas or product plans by interviewing the user, inferring the PRD name, and writing docs/NNNN-name/prd.md.
---

# Tiger PRD

Use this skill to create a concise, decision-ready PRD. Default to creating a new PRD directory and file at `docs/NNNN-name/prd.md`, where `name` is inferred from the user chat and normalized to kebab-case. Modify an existing PRD only when the user explicitly asks to update, revise, edit, or rewrite an existing one.

Documentation-only scope: write or update the PRD document only. Do not implement the feature, change product code, add tests, install dependencies, run migrations, or modify non-PRD files as part of this skill.

## Workflow

1. Ground in the repo before asking questions.
   - Inspect existing `docs/` PRDs, product docs, issue templates, roadmap files, or nearby domain files when they can answer naming, format, scope, or implementation-context questions.
   - Do not ask the user for information that can be discovered from the repo.

2. Infer the PRD identity.
   - Infer a short product or feature name from the conversation.
   - Normalize it to lowercase kebab-case for the folder suffix.
   - Scan `docs/` for existing `NNNN-*` folders and choose the next available zero-padded number. Use `0001` if no matching folder exists.
   - Use the path `docs/NNNN-name/prd.md` unless the user explicitly provides another path.
   - Do not reuse an existing PRD directory just because the inferred name is similar; create a new numbered directory unless the user explicitly asks to modify that existing PRD.

3. Grill the user one question at a time.
   - Ask exactly one material question per turn.
   - Provide a recommended answer with every question.
   - Walk dependencies in order: goal, users, problem, scope, workflows, requirements, acceptance criteria, risks, and open questions.
   - Keep asking until success criteria, audience, in-scope behavior, non-goals, constraints, and unresolved decisions are clear enough to write the PRD.
   - If the user asks to proceed with ambiguity, state the assumptions and continue.

4. Write the PRD.
   - Create the target directory if needed.
   - Create a new `prd.md` by default.
   - Update an existing `prd.md` only when explicitly requested.
   - When updating, preserve existing relevant PRD content and avoid overwriting user-authored details unless the user asked for a rewrite.
   - Stop after writing the PRD; do not proceed into design, tasks, or implementation unless the user makes a separate request.

## Default PRD Structure

Use this structure unless the repo has an established PRD template or the user asks for a different format:

```markdown
# <Product or Feature Name> PRD

## Background
<Problem context and why it matters.>

## Goals
- <Outcome the product should achieve.>

## Non-Goals
- <Explicitly excluded behavior or scope.>

## Target Users
- <Primary user or stakeholder.>

## Core Workflows
- <User workflow or story.>

## Functional Requirements
- <Requirement phrased as product behavior.>

## Acceptance Criteria
- <Observable condition that proves the PRD is satisfied.>

## Risks and Open Questions
- <Known risk, dependency, or unresolved decision.>
```

## Writing Guidelines

- Keep the PRD product-focused. Do not turn it into an engineering design doc unless the user asks or the requirement depends on technical constraints.
- Prefer crisp bullets over long prose.
- Use concrete, testable requirements. Avoid vague verbs like "support" without stating the visible behavior.
- Make non-goals explicit when they prevent likely scope creep.
- Add risks and open questions instead of silently deciding unresolved product tradeoffs.
- Include implementation notes only when they are already known constraints or necessary context for acceptance.

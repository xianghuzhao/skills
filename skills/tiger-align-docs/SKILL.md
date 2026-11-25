---
name: tiger-align-docs
description: Compare behavior documented in docs/ with current code in both directions, report meaningful gaps, and ask how to resolve them.
disable-model-invocation: true
---

# Tiger Align Docs

Treat all project files as read-only throughout the review, including after the user chooses how to resolve a finding. Record those decisions only in the conversation and leave edits to subsequent work.

Review only `docs/` and current code. Skip `dev-docs/` because its development history may differ from current behavior.

Read the [Tiger documentation layout](../_shared/tiger-docs-layout.md) and applicable project instructions. Compare the documentation index with the maintained topic pages on disk. Report structural issues separately and include unindexed pages in the review. If the formal documentation directory is missing, explain the coverage limitation and stop.

Trace documented behavior through relevant code entry points, then review implemented capabilities and rules against the topic pages. Group findings by behavior:

1. **Documented feature without an identified implementation.** Cite the promised behavior and the entry points or code areas checked.
2. **Contradictory behavior.** Give the condition under which the documentation and implementation predict different outcomes. Cite the relevant section and code path and describe both outcomes. Flag inconclusive evidence as an open question.
3. **Important undocumented behavior.** Identify an implemented capability readers could miss or a rule whose omission permits different reasonable expectations for users or maintainers. Cite the implementation and the documentation area checked. Explain the concrete consequence. Omit routine implementation details.

Present all findings together with stable numbers, impact, and a recommended disposition for each. Ask how to handle them, accepting individual or grouped answers. Summarize confirmed decisions and outstanding items by finding number and finish. If no discrepancies are found, summarize the scope checked and finish.

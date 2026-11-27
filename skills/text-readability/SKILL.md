---
name: text-readability
description: Use when writing or editing text intended for long-term retention and human reading, including source code, maintained scripts, configuration, documentation, and examples. Skip commands and scripts used only for temporary execution.
---

# Text readability

An ad hoc shell command or throwaway script can use whatever layout is convenient for the run. If the same command is recorded in a README for future readers, apply these rules to the documented example.

Put independent statements on separate lines. Keep lines that perform one step together, and leave a blank line when the purpose changes. For example, in a script that validates input, computes a result, and writes it, give each step its own group.

Keep a simple assignment or short list on one line when it is easy to scan. Expand a dense expression or nested structure across lines so readers can follow its parts.

Group configuration settings by purpose and separate distinct groups with a blank line where the format permits. In documentation, start a new paragraph when the topic changes. Apply the same layout rules to embedded examples as to standalone code or configuration.

Hand-maintained JSON and test snapshots kept for human review both benefit from readable layout. Preserve the tool-defined layout of machine-only artifacts such as lockfiles and minified bundles.

Follow the project's formatter and preserve meaningful whitespace and record boundaries. For example, keep each JSONL record on a single line. When editing an existing file, limit regrouping and line wrapping to the logical block touched by the task.

Before saving, remove trailing spaces and tabs from every line, including blank lines. End a nonempty file with a single newline immediately after its last content line.

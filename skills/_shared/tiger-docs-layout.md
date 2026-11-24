# Tiger documentation layout

Paths below are relative to the target project.

- `docs/` holds long-lived project documentation. Authoritative topic pages record confirmed target behavior and rules, including features pending implementation.
- `docs/README.md` is the sorted index of maintained documents, including topic pages in subdirectories. Use relative links and short descriptions. Omit the index itself and generated or binary assets.
- `dev-docs/<number>-<kebab-case-description>/` holds one work item. Its `spec.md` records scope, implementation gaps and acceptance criteria, linking to authoritative topic sections. Its sibling `tasks.md` tracks execution. Keep formal documentation usable without local work items.
- Prefer keeping work items local and outside Git unless project collaboration conventions call for sharing them. Leave ignore configuration to the project.

## Organizing topic pages

Follow the project's existing categories and names. Use these common categories where they fit, and add others as needed:

- **Product** describes what users can do, the business rules that apply, and the outcomes they can expect.
- **Architecture** describes system components, their responsibilities, and how they interact.
- **Data model** defines core entities, their relationships, and constraints on their data.
- **Glossary** defines project-specific or easily confused concepts, their preferred names, and useful aliases.
- **Coding standards** capture team conventions for writing and organizing code.
- **API** defines the contracts callers can rely on, including inputs, outputs, and error behavior.
- **Testing** describes the test strategy, the responsibilities of each test layer, and how to verify critical behavior.
- **Release** describes how versions are delivered and which rules govern releases, upgrades, and rollbacks.

Give each topic one authoritative home. Link to it from other pages, adding a short summary where useful. When the project has no naming convention, use `docs/<kebab-case-topic>.md`. Group related pages in a subdirectory when organizing them by topic, module, or business domain makes them easier to find or clarifies ownership.

During review, report a documentation gap when specific behavior needs an explanation. An unused category alone is not a gap.

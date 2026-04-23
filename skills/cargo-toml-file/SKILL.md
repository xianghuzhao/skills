---
name: cargo-toml-file
description: Use before adding, removing, or editing Rust Cargo dependencies in Cargo.toml files, including workspace dependencies, dependency features, versions, path/git dependencies, and dev/build dependency sections.
---

# Cargo.toml Dependency Changes

Use this workflow whenever a task requires changing Cargo dependency metadata.

## Core Rules

- Do **not** add a dependency unless code or tests in the project will use it.
- Prefer `cargo add` and `cargo rm` over hand-editing `Cargo.toml` when they can express the change cleanly.
- Preserve the repository's existing dependency style, including workspace-level dependencies, alphabetical ordering, comments, and table formatting.
- Put dependencies in the narrowest correct section: `dependencies`, `dev-dependencies`, `build-dependencies`, or the matching target-specific table.
- Keep default features enabled unless the project already disables them for that crate or the requested behavior requires `default-features = false`.
- Add only the features that are required by the implementation. Do not guess features from memory when correctness depends on them; check crate metadata with Cargo tooling.
- Do not introduce `path` or `git` dependencies unless the user requests them or the repository already uses that pattern for the same dependency source.

## Adding Dependencies

1. Inspect the workspace layout first: root `Cargo.toml`, member crates, and any existing `[workspace.dependencies]`.
2. If the project centralizes versions in `[workspace.dependencies]`, add or update the crate there and reference it from member crates with `{ workspace = true }`.
3. Use `cargo add <crate>` from the target package directory when there is a single package.
4. In a workspace, use `cargo add <crate> -p <package>` when the package is clear; otherwise inspect package names before changing files.
5. Use `cargo add <crate> --dev` or `--build` for test-only or build-script-only dependencies.

## Removing Dependencies

- Remove a dependency only after confirming it is no longer referenced by source, tests, examples, benches, build scripts, or feature definitions.
- Use `cargo rm <crate>` when possible.
- If removing a workspace dependency, confirm no member still references it.

## Versions and Features

- Prefer the latest compatible stable version that `cargo add` selects unless the user requested a specific version or the project pins versions intentionally.
- Respect existing minimum-supported Rust version constraints if the project documents them.
- If a crate is already present, extend the existing dependency entry instead of creating a duplicate.
- When changing features, verify feature names with `cargo info <crate>` or equivalent Cargo output if they are not already present in the project.

## Validation

After dependency changes, run the smallest useful verification:

```bash
cargo check
```

For workspaces or changes affecting tests/features, prefer:

```bash
cargo check --workspace
cargo test --workspace --no-run
```

If validation cannot run because dependencies cannot be downloaded or the environment blocks network access, report that explicitly.

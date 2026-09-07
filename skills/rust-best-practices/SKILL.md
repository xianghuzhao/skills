---
name: rust-best-practices
description: Develop, refactor, and review Rust application code, including services, CLIs, and workers. Apply personal defaults for async I/O, application boundaries, error handling, workspace organization, and validation while respecting existing project choices.
allowed-tools: Read, Edit, Write, Bash, Grep, Glob, Task
---

# Rust Application Practices

## Start with the Project

- Inspect repository instructions, Cargo manifests, the toolchain, edition, minimum supported Rust version (MSRV), neighboring code, and CI before choosing an implementation.
- Use the defaults below for new projects without an established choice. Preserve existing frameworks, synchronous architectures, dependency conventions, and release policies unless the task calls for changing them.
- Prefer asynchronous APIs for network, file, database, and subprocess I/O, including in small CLIs. Keep pure computation, in-memory transformations, and configuration parsing synchronous.
- Add dependencies only when code or tests need them. Do not scaffold the entire stack, migrate unrelated code, upgrade the toolchain, or create CI merely because this skill is loaded.
- Read only the relevant reference when the task involves:
  - Async I/O, locks, background tasks, cancellation, or shutdown: [Async and I/O](references/async.md).
  - Startup settings, environment variables, or CLI overrides: [Configuration](references/configuration.md).
  - HTTP services, CLIs, outgoing requests, or logging: [Application Boundaries](references/app-boundaries.md).
  - SQLx queries, transactions, migration authoring and deployment, status and rollback commands, or offline builds: [Database](references/database.md).

## New Application Defaults

| Need | Default |
|------|---------|
| Async I/O runtime | Tokio; applications without async work do not need a runtime |
| Application error reporting | `anyhow` with actionable context |
| Errors callers must distinguish | Typed errors, using `thiserror` when useful |
| HTTP API | Axum; add Tower / tower-http middleware as needed |
| Non-trivial CLI parsing | clap derive |
| Outgoing HTTP | Reused asynchronous reqwest Client |
| Serialization | serde; serde_json when JSON is needed |
| Structured diagnostics | tracing and tracing-subscriber |
| Relational database access | SQLx; choose the database product for the project's needs |
| Configuration | Direct typed parsing for simple settings; config for layered sources |

Do not hardcode dependency versions from this document. Check current compatible APIs and features against the project's toolchain. For Cargo dependency changes, follow the companion `cargo-toml-file` skill when available; it owns the dependency editing workflow.

## Errors and Panics

- Choose error types by the caller's needs, not simply by whether a file belongs to a binary or library. Preserve typed failures where callers branch on them; use anyhow at application orchestration and reporting boundaries.
- Return errors for invalid input, missing configuration, network failures, and I/O failures. Report startup errors and exit unsuccessfully rather than using `expect` for missing external resources.
- Add context at meaningful operation boundaries, preserving the source error. Include the operation and safe identifying information, not secrets. Do not replace failures with successful defaults unless the behavior explicitly requires a fallback.
- An internal invariant may use `expect` when code establishes the condition and all relevant paths preserve it. Explain why the condition holds; a message saying that user input or configuration "must exist" is not proof.
- Tests may use `unwrap` or `expect` to make unexpected failures fail the test. Neither method is a general substitute for handling production errors.
- Translate errors into HTTP responses or CLI diagnostics at the application boundary; avoid logging the same propagated error at every layer.

## Ownership, Types, and Readability

- Borrow when a function only reads data (`&str`, `&[T]`). Accept owned values when storing or transferring ownership; use `impl Into<String>` when callers benefit from conversion at an ownership boundary.
- A clone can be appropriate for independent ownership or shared handles such as `Arc`, connection pools, and HTTP clients. Consider its actual cost before changing the ownership design.
- Use `Cow` only when returning either borrowed or owned data avoids meaningful allocation without complicating the interface unnecessarily.
- Use enums and newtypes for meaningful domain distinctions. Validate constrained values at construction and keep fields private where needed. A newtype does not itself enforce numeric validity or distinguish two parameters of the same type, such as source and destination account IDs.
- Use builders when optional settings or construction constraints justify them. Keep required values and build-time errors consistent; prefer a direct constructor for simple inputs.
- Choose loops or iterator chains for clear control flow. Boolean parameters are reasonable when their meaning is clear; use named options or enums when call sites become ambiguous.
- Split functions by responsibility, not a line-count threshold. Avoid speculative traits, builders, and helper layers introduced only to satisfy a style preference.
- Use `#[must_use]` when ignoring a return would silently discard the intended effect, such as a returned transformed value. `Result` already carries this annotation; repeating it without a useful message usually adds little.
- Measure performance before changing algorithms or ownership for speed. Reserve capacity when a useful size is known, but do not treat every allocation or unreserved collection as a defect.

## Application and Workspace Structure

- Start with one package and focused modules. Keep `main` responsible for wiring and startup; extract a `lib` when integration tests or reuse need an importable interface. Split into workspace members when real dependency or ownership boundaries justify it.
- Default to private modules and items. Widen to `pub(super)`, `pub(crate)`, or `pub` only as callers require; re-export the intended interface without exposing all implementation modules.
- For a new workspace representing one application, define the shared package version in root `[workspace.package]`; members explicitly inherit it with `version.workspace = true`. Independently released packages may keep their own versions.
- Centralize dependency version requirements in root `[workspace.dependencies]` for new workspaces. Members explicitly inherit only the dependencies they use, in the appropriate normal, dev, build, or target-specific section. A root declaration alone does not add a dependency to a member.
- Package version inheritance and dependency inheritance are different. Do not use `version.workspace = true` inside a dependency entry. Preserve an existing workspace's conventions.
- Keep the application's shared `Cargo.lock` under version control. In a virtual workspace, set the resolver explicitly to one supported by the chosen toolchain and edition.

Example manifest fragments, with the root's workspace membership already configured:

```toml
# Root Cargo.toml
[workspace.package]
version = "0.1.0"

[workspace.dependencies]
serde = "1"
```

```toml
# Member Cargo.toml
[package]
name = "my-app-core"
version.workspace = true

[dependencies]
serde = { workspace = true, features = ["derive"] }
```

See [Cargo workspace inheritance](https://doc.rust-lang.org/cargo/reference/workspaces.html) for supported fields and toolchain requirements.

## Tests, Documentation, and Checks

- Test observable outputs, specific failure categories, and important side effects rather than only `is_ok()`. Add regression coverage for changed behavior and bug fixes.
- Unit tests can live alongside code; integration tests in `tests/` exercise the public boundary. Keep core logic testable without starting the server. Use real database tests for SQL and transaction behavior when relevant.
- Cover configuration precedence, error translation, and relevant timeout, cancellation, and shutdown behavior. Avoid tests tied to private implementation steps or arbitrary sleeps.
- Document contracts that callers need: units, invariants, ownership, errors, and intentional panics. Keep examples valid with required imports and setup; do not add boilerplate argument documentation that merely repeats a signature.
- Check API stabilization against MSRV. For example, `std::assert_matches!` requires Rust 1.96 and an import or qualified call; use `assert!(matches!(...))` on older supported toolchains. See the [standard library documentation](https://doc.rust-lang.org/std/macro.assert_matches.html).
- New projects should pass formatting, Clippy with `-D warnings`, and relevant tests. Existing projects follow their CI and lint configuration. Start with affected packages and broaden when shared behavior changes.
- Choose workspace, target, and feature coverage deliberately; do not blindly enable `--all-features` when combinations are incompatible. Include doctests when changing documented examples.

Baseline commands for a single package, adapted to the repository's required checks:

```bash
cargo fmt -- --check
cargo clippy -- -D warnings
cargo test
```

Report checks that could not run and why. Do not silently weaken lint settings or present unexecuted checks as passing.

# Database

Read when implementing SQLx access, transactions, migration authoring and deployment, database management commands, or offline query checking. The migration conventions below are defaults for new projects; preserve existing migration tools, file layouts, and release policies unless changing them is part of the task.

## Access and Transactions

- Default to SQLx for new relational database access. Choose PostgreSQL, SQLite, or another supported database from deployment, concurrency, and persistence requirements rather than assuming one product.
- Reuse an async pool created at application startup. Configure connection limits and acquisition timeouts for the workload; do not create a pool per operation or hide it behind an unnecessary mutex.
- Bind values as parameters. For dynamic queries, use binding APIs such as QueryBuilder's `push_bind`; identifiers and SQL fragments require controlled construction, not string interpolation of untrusted input.
- Keep SQL near the persistence boundary. Return typed application data and errors rather than leaking SQLx row handling throughout handlers and domain logic.
- Use transactions for changes that must succeed together. Explicitly commit successful work, handle failures, and avoid holding a transaction open over unrelated network I/O.

## Migration Files and Delivery

- Keep versioned SQL files in root `migrations/` for both single-package projects and workspaces. All crates sharing one database use one migration sequence, not independent sequences for the same schema.
- For new projects, use increasing timestamp versions followed by descriptive names. Check ordering and duplicate versions when merging branches; resolve conflicts before deployment without renumbering already applied migrations.
- For reversible changes, write matching `.up.sql` and `.down.sql` files with the same version and description. For irreversible changes, use a one-way `.sql` migration and document why it is irreversible and how recovery works. Do not provide an empty down migration that falsely reports success.
- Keep applied migrations unchanged, including their down files. Correct released changes with new migrations rather than altering their history.
- Create files with the project's compatible SQLx CLI. Run from the project/workspace root with an explicit source directory. For example, a reversible change uses:

```bash
cargo sqlx migrate add --source migrations --timestamp --reversible add_user_preferences
```

For a one-way change, use the CLI's explicit simple-migration option if supported by that version, or create the correctly named `.sql` file. Do not assume omitting `--reversible` produces a one-way migration: the CLI can infer the previous migration style.

- Embed migrations with `sqlx::migrate!` so the application and migration set ship together. Do not depend on runtime working-directory lookup or a separately deployed SQL directory by default.
- The macro's migration path is relative to the crate manifest containing the invocation. Put directory tracking in that crate's `build.rs`, merging with an existing build script instead of replacing it. Emit `cargo:rerun-if-changed=...` for the actual migration directory so added files trigger recompilation.

| Macro-owning crate | Macro path | Build-script directory to track |
|--------------------|------------|---------------------------------|
| Project root | `sqlx::migrate!("./migrations")` | `migrations` |
| `crates/app` in a workspace | `sqlx::migrate!("../../migrations")` | `../../migrations` |

The workspace example assumes exactly that layout. Resolve the path from the actual crate location; embedding also requires the migration files to be present in the build context. See [SQLx migration embedding and build tracking](https://docs.rs/sqlx/latest/sqlx/macro.migrate.html).

## Database Management Commands

- Expose migrations as independent application subcommands. Reuse the project's configuration sources, project-prefixed environment variables, and diagnostics. Initialize only migration-relevant settings, logging, and database access; do not start HTTP listeners, background workers, or unrelated clients.
- Use async database operations. Ordinary service startup must not automatically apply migrations.

| Command | Contract |
|---------|----------|
| `db migrate` | Validate migration history and apply pending migrations in order. Succeed without replaying SQL when nothing is pending. |
| `db status` | Read-only report of version, description, applied/pending state, and history anomalies. Never create a database or migration table, or apply migrations. |
| `db rollback` | Revert exactly the latest applied migration. Fail if it is irreversible or its down file is missing; never skip it to revert an older migration. Report no work when no migrations have been applied. |

- If the migration table does not exist, status reports an uninitialized history and the embedded migrations as pending. Connection and permission failures remain errors; do not disguise them as an empty database. Inspect existence without calling helpers that create the table, and avoid connection options that create a database during status checks.
- Detect missing applied versions, checksum mismatches, and failed/dirty migration states. Status reports the anomaly and exits unsuccessfully; migrate and rollback stop without bypassing it. Do not automatically rewrite history, ignore missing versions, or mark failed migrations as successful.
- Preserve database-appropriate migration mutual exclusion. For rollback, determine and validate the latest applied version and perform the reversal in the same exclusive scope, using the same connection where the lock is connection-scoped. A precheck followed by separately locked execution has a race.
- Check the actual SQLx version's APIs rather than copying CLI semantics. Status implementations may create migration tables, and undo implementations may skip migrations without down files or revert multiple versions above a target. Ensure the application wrapper enforces the contracts above and checks history before modifying it.
- Do not silently disable locking to work around a backend limitation. Use an explicit equivalent serialization mechanism where the backend lacks the expected lock support.

## Deployment, Transactions, and Recovery

- Default to a maintenance window: stop the old service and related writers, run the migration command, verify success, then start the new version. A migration failure leaves the service stopped; do not automatically roll back or continue starting the new version.
- Use the new release's embedded migration set. For rollback, use an artifact containing the applied migration and its down file, then deploy an application version compatible with the resulting schema.
- The migration command's success covers its own work; deployment orchestration owns stopping and starting services. Projects requiring continuous availability or rolling releases need an explicitly staged, backward-compatible migration sequence instead of assuming a maintenance window.
- Organize SQL according to the database engine and SQLx version's transaction support. Do not assume an entire batch of migrations is atomic. Isolate statements that require execution outside a transaction and use only the backend/version-supported transaction opt-out mechanism.
- For non-transactional work, document possible partial completion and the steps needed to inspect and recover it. Do not blindly retry a partially completed migration or edit bookkeeping to hide the failure.
- Include small, predictably bounded data changes in SQL migrations when appropriate. Run large backfills as separate, batched, restartable jobs that can resume progress without duplicating effects. Specify their order relative to schema changes and which completion checks must pass before service startup or later constraint changes.
- Document each down migration's structural and data effects, including data written after up ran. Recreating a column does not recover discarded values. For destructive changes, state the necessary data recovery method, such as a tested backup/restore procedure; a down file alone is not proof of lossless recovery.

## Static and Dynamic Queries

- Prefer `query!` / `query_as!` for static SQL in new projects. These check against a schema during preparation; they do not prove business behavior or transaction correctness.
- Use runtime `query` / `query_as` or QueryBuilder for dynamic SQL, with parameter binding and database integration tests.
- Preserve an existing project's query style unless migration is requested. Do not force macro adoption as a side effect of a small fix.

## Offline Metadata Workflow

- Commit `.sqlx` metadata for compile-checked queries. Prepare it against a disposable development/test database with the project's migrations applied and the correct database engine.
- Regenerate metadata when queries or their schema change. Include the relevant workspace packages, targets, and compatible feature combinations; do not assume default-target preparation covers test-only queries.
- Apply and validate the intended migration set in the isolated test database before refreshing query metadata. Query preparation does not replace migration validation.
- Validate metadata freshness using `cargo sqlx prepare --check` against the matching test schema. Then verify that an offline build succeeds without requiring database access.
- Use a SQLx CLI version compatible with the project's SQLx version and required database backend. Do not automatically install a global tool merely on loading this reference.

Typical commands after configuring `DATABASE_URL` for an isolated test database and applying its migrations:

```bash
# Refresh metadata; inspect the generated diff.
cargo sqlx prepare --workspace

# Check committed metadata against the prepared schema.
cargo sqlx prepare --check --workspace

# Verify compilation uses committed metadata without contacting a database.
SQLX_OFFLINE=true cargo check --workspace
```

For queries behind non-default features or test/example targets, adapt preparation and verification flags to those same targets and feature sets. Forward Cargo flags after `--` when using `cargo sqlx prepare`.

- Application settings may use `MY_APP__DATABASE__URL`. SQLx tooling independently reads `DATABASE_URL`; explicitly supply it through the development/CI environment, without logging credentials or treating it as a required unprefixed application setting.
- `SQLX_OFFLINE=true` affects query macro compilation, not runtime connections. Metadata freshness checking needs the matching database; offline compilation alone does not establish freshness.
- Keep migration/preparation/checking as documented development and CI checks; do not generate a new CI system solely to apply this skill.

## Tests

- Exercise queries, migrations, constraints, and commit/rollback behavior against an isolated database of the intended engine. Do not substitute SQLite for PostgreSQL-specific behavior.
- Assert returned values and persisted state, including relevant constraint failures, rather than merely checking that a query returned `Ok`.
- Keep database integration tests separate from pure domain tests. Make setup reproducible and ensure cleanup respects isolation.

For migration changes, cover the relevant lifecycle cases:

- Apply the full sequence to an empty database, and upgrade a previous schema containing representative historical data. Check the intended schema, data, and migration records.
- Exercise reversible changes with up, down, and up again. Verify actual restoration and document intentional data loss rather than merely asserting that commands exit successfully.
- Run migrate again with no pending changes and confirm it does not replay applied SQL.
- Verify status makes no changes with or without the migration table, and does not create a missing database. Verify permission/connection errors and history anomalies are reported correctly.
- Verify rollback rejects an irreversible latest migration or missing down file without touching earlier versions; check no-history behavior and single-step reversal with pending migrations present.
- Exercise checksum mismatches, missing history files, failed/dirty state, and execution failures. For non-transactional SQL, inspect partially completed state and test the documented recovery procedure. Test concurrent migration/rollback exclusion where applicable.
- Add a migration file without changing Rust sources, rebuild, and verify the embedded migration set includes it. Include the workspace path case when applicable.
- After migration validation, refresh/check `.sqlx` and verify offline compilation for the relevant targets and feature sets.

See [SQLx query macros](https://docs.rs/sqlx/latest/sqlx/macro.query.html), [Migrator](https://docs.rs/sqlx/latest/sqlx/migrate/struct.Migrator.html), and the [SQLx CLI workflow](https://github.com/launchbadge/sqlx/blob/main/sqlx-cli/README.md).

# Async and I/O

Read when implementing I/O, shared state, background tasks, cancellation, or shutdown.

## I/O Boundaries

- Prefer async I/O even in small tools: async reqwest for HTTP, SQLx for databases, and Tokio file, network, and subprocess APIs where applicable. Await subprocess completion instead of blocking the runtime thread.
- Propagate async through callers that need to await I/O. Keep parsing, validation, and ordinary computation synchronous; an `async fn` does not make blocking operations non-blocking.
- Use one clear runtime entrypoint. Do not create nested runtimes or scatter `block_on` calls throughout an async application.
- Preserve an established synchronous architecture unless the task calls for migration. When adding async behavior, make its boundary explicit rather than partially converting unrelated modules.
- If an operation only offers a blocking API, move suitable bounded work to `spawn_blocking`. Handle both the task's `JoinError` and any error returned by the operation; do not use `.await.unwrap()` as the default.
- CPU-heavy work also needs scheduling consideration. Bound concurrent computations with a semaphore or an appropriate compute pool; async syntax alone does not prevent starvation. Use a dedicated thread for persistent blocking loops when appropriate.
- Started `spawn_blocking` operations cannot be aborted. A timeout on waiting does not stop them; account for this in resource ownership and shutdown.

## Shared State and Backpressure

- Keep critical sections small. A standard mutex can be appropriate for short in-memory access with low contention if its guard is released before `.await`; use a lexical scope to make this clear.
- Use an async mutex when holding the lock across awaited work is actually necessary. First consider whether copying the needed state or moving ownership to a task removes that need. Do not hold locks over slow I/O merely for convenience.
- Share state deliberately. `Arc` shares ownership but does not make its contents thread-safe; avoid a single coarse lock around unrelated application state.
- Bound in-flight requests and work queues. Choose capacity and saturation behavior from the workload instead of spawning one task per item without a limit.
- Acquire concurrency permits before spawning work when the aim is to bound the number of tasks as well as active operations. A semaphore inside each of an unlimited number of spawned tasks does not bound the waiting tasks.

## Task Lifetime, Cancellation, and Shutdown

- Keep ownership of important background tasks through handles, a `JoinSet`, or an existing task tracker. Observe task failures and decide whether they should fail the application or be reported and recovered.
- Dropping a `JoinHandle` detaches its task; it does not cancel it. Do not equate losing the handle with stopping work.
- Timeouts and losing branches of `select!` may drop futures. Check cancellation safety for the specific operation and preserve partial progress where retrying could lose data or duplicate side effects.
- Cancellation of a local future does not prove that a remote request, database write, or subprocess stopped. Define cleanup and retry behavior accordingly.
- Make shutdown explicit: stop accepting work, signal cancellation, allow in-flight work to finish within the application's shutdown budget, and observe task completion. Handle tasks that cannot be stopped cooperatively.
- For child processes, choose an explicit terminate-and-wait policy where required; do not assume dropping a process handle kills the process.
- Test timeout and shutdown behavior with controlled synchronization and Tokio's test-time facilities where supported, rather than long sleeps or timing-sensitive assertions.

## Official References

- [Tokio shared state](https://tokio.rs/tokio/tutorial/shared-state)
- [Blocking work and cancellation limits](https://docs.rs/tokio/latest/tokio/task/fn.spawn_blocking.html)
- [select! cancellation safety](https://docs.rs/tokio/latest/tokio/macro.select.html#cancellation-safety)
- [Graceful shutdown](https://tokio.rs/tokio/topics/shutdown)

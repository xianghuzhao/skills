# Application Boundaries

Read when building HTTP APIs, CLIs, outgoing HTTP calls, or structured diagnostics.

## HTTP Services

- Default to Axum for new HTTP APIs. Keep handlers focused on extraction, validation, invoking application logic, and response/error conversion.
- Pass shared clients, settings, and pools through typed application state. Prefer direct constructor or function parameters to a dependency injection container.
- Keep domain logic independent of HTTP status codes and framework extractors. Define an application error mapping at the transport boundary; return useful client errors without exposing internal error chains or secrets.
- Add Tower / tower-http middleware for actual requirements such as tracing, timeouts, or body limits. Check layer ordering and error conversion; a middleware error is not automatically an application JSON response.
- Test routing, status, response content, and error conversion without binding a real listening socket where in-process requests suffice. Test actual transport behavior separately when relevant.

## CLIs

- Use clap derive for non-trivial arguments and subcommands. Parse arguments once; dispatch to application functions rather than embedding business logic in parser definitions.
- Use async operations for I/O and await them from a Tokio entrypoint when needed. Pure computation does not require an async runtime.
- Reserve stdout for requested results, including machine-readable output; send diagnostic logging and failures to stderr. Preserve meaningful nonzero exit status on failure.
- Keep explicit CLI overrides separate from defaults when merging configuration; follow [Configuration](configuration.md).
- Test argument errors, output content, and exit behavior. Check that diagnostics do not contaminate structured stdout.

## Outgoing HTTP

- Reuse an async reqwest Client configured at application construction. Its connection pool is shared by client clones; avoid rebuilding it for every request or adding an unnecessary outer lock.
- Set request/connect timeouts appropriate to the operation. Handle unsuccessful HTTP status codes explicitly before treating a response body as success; preserve useful safe error details when the API provides them.
- Bound concurrency and response sizes when the workload can be large. Stream large bodies rather than buffering them solely for convenience.
- Retry only when the operation's semantics permit it. Use bounded attempts/backoff and a total time budget; do not blindly retry writes whose remote outcome is unknown.

## Logging and Serialization

- Use typed serde structures for stable input/output shapes. Use `serde_json::Value` for genuinely dynamic JSON rather than as the default long-lived application model.
- Initialize tracing-subscriber once at the executable boundary. Internal modules emit events and spans without configuring global subscribers.
- Include useful fields such as operation, request ID, or job ID when available. Log an error at the boundary that handles or reports it rather than at every propagation step.
- Exclude tokens, passwords, connection URLs containing credentials, and sensitive payloads from logs. Configure `#[instrument]` with appropriate skipped arguments or explicitly chosen fields.
- In async functions, use `#[instrument]` or `.instrument(span)` rather than holding a `Span::enter()` guard across `.await`, which can misattribute events.
- For CLIs, explicitly configure the subscriber writer to stderr. Do not install a telemetry exporter or monitoring backend unless required by the application.

## Official References

- [Axum](https://docs.rs/axum/latest/axum/)
- [clap](https://docs.rs/clap/latest/clap/)
- [reqwest Client](https://docs.rs/reqwest/latest/reqwest/struct.Client.html)
- [tracing](https://docs.rs/tracing/latest/tracing/)

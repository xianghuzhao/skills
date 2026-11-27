# Rust style for applications and reusable APIs

Follow the project's established conventions and the wider Rust ecosystem. Apply library ergonomics and compatibility rules in proportion to the consumers of an interface. A helper private to one service can use the simplest signature its callers need. [API design in context](https://google.github.io/styleguide/rustguide.html#apply-api-design-rules-in-context)

## Modules, imports, and formatting

- Name a leaf module after its main type when it has one. Keep implementations in leaf modules and use parent modules for declarations, re-exports, and small amounts of wiring.
- Group imports from one module per use statement. Prefer explicit names. Common glob exceptions include inline tests, variants in enum-specific code, and deliberate facade re-exports. Follow established dependency preludes where appropriate.
- Scope a trait import to the function using it unless it is used throughout the file. Qualify free function calls with their module when it clarifies origin. Import types directly unless names collide or a shared module qualifier identifies a family of types. [Code organization](https://google.github.io/styleguide/rustguide.html#code-organization)
- Use the repository's formatter and supported settings. Reserve rustfmt skips for generated code or meaningful visual alignment, such as matrix rows. [Formatting](https://google.github.io/styleguide/rustguide.html#code-formatting-and-aesthetics)

## Ownership and signatures

- Borrow the underlying value for read-only access. For example, accept `Option<&T>` when the caller can lend an optional value. Clone for independent ownership when the copying cost is acceptable. For shared handles and lock poisoning see [Async and I/O](async.md). [Ownership](https://google.github.io/styleguide/rustguide.html#ownership-and-borrowing)
- A reusable API that only traverses input can accept `IntoIterator` and return an iterator to avoid forcing collection. A slice and a vector are useful application interfaces when callers already have contiguous data or need owned results or indexing.
- Use an enum for an ambiguous Boolean argument. Use `Option` for an anticipated absence with no useful diagnosis, and `Result` when callers need a reason. Apply the [application error rules](../SKILL.md) to external failures. [Function inputs and outputs](https://google.github.io/styleguide/rustguide.html#function-inputs-and-outputs)

## Types and traits

- Use a newtype for distinct semantics, validation, or representation hiding. A type alias suffices for another spelling of the same type. Name fields when callers would have to remember tuple positions. [Types](https://google.github.io/styleguide/rustguide.html#types)
- Start with inherent methods. Define traits for generic bounds, dispatch, or a deliberate extension API. Coordinate extension methods with the extended type's owner and name the trait with an `Ext` suffix. In reusable library parameters prefer generics when specialization is useful; consider dynamic dispatch for heterogeneous values or code size.
- Derive standard traits when their semantics fit. Use standard conversion and parsing traits. Pair a primary conversion with a discoverable constructor. Provide Default for options with meaningful defaults.
- Add application trait implementations as needed. For library values consider Clone and, if sustainable as an API promise, Copy, including for small errors. Use Eq only for reflexive equality and Ord for a total order. Keep hashing consistent with equality for map keys. [Traits](https://google.github.io/styleguide/rustguide.html#traits)

## Naming, documentation, and macros

- Use conventional conversion prefixes: `as_` for a cheap view, `to_` for a conversion borrowing the receiver, and `into_` when consuming it. Prefer `new` for construction and `try_new` for fallible construction. Give important lifetimes descriptive names and choose short generic names that convey their role. Spell out unfamiliar project abbreviations. [Naming](https://google.github.io/styleguide/rustguide.html#naming)
- Document public contracts with rustdoc, including panic conditions, safety obligations, and representative usage. Explain the unit's purpose and use complete sentences and links to related items. [Documentation](https://google.github.io/styleguide/rustguide.html#when-and-how-to-write-documentation)
- Prefer ordinary Rust abstractions. For repeated syntax, try a declarative macro before introducing a generator or procedural macro; weigh the impact on diagnostics and code navigation. [Macros](https://google.github.io/styleguide/rustguide.html#macros)

## Safety and API evolution

- Use stable features. Where safe Rust cannot express the operation, isolate unsafe code behind a checked interface. Document caller or implementer obligations in a Safety section and explain at each unsafe operation how those obligations are met with a SAFETY comment. Justify performance-driven unsafe code with measurements.
- Expose fields directly when doing so fits the representation contract. Use accessors to preserve invariants, and consider a scoped closure when exposing a lock guard would leak synchronization details. Pair len with is_empty. Treat another crate's hidden documentation items as private.
- Use operator overloads with their conventional semantics. Expose a const function only when callers can depend on const evaluation over time. Initialize explicitly at startup.
- For enums and public-field structs consumed across teams, consider non_exhaustive when variants or fields may grow. An intentionally closed domain or a commitment to migrate all consumers can justify exhaustive access. Give callers a construction path for extensible structs.
- Apply must_use when ignoring the result loses the operation or critical information. Annotate the type for consistently lazy or significant results. An annotated return type already provides the warning. [Other Rust features](https://google.github.io/styleguide/rustguide.html#other-rust-features)

## Implementation and tests

- Add type annotations when readers would otherwise have to trace inference. Use pattern matching to bind a checked value. For an invariant the compiler cannot express, an expect message should explain what establishes it. When matching an extensible enum, handle known variants explicitly and reserve the fallback for future variants.
- Put useful failure context into assertion messages. Choose loops for branching or side effects and iterator adapters for direct operations such as any. Use format! for multi-part strings and write! to append to an existing destination.
- Propagate recoverable failures explicitly. Panic catching is only a last defensive boundary on unwinding builds; correctness must hold without it. [Implementation concerns](https://google.github.io/styleguide/rustguide.html#implementation-concerns)
- Use the project's test harness and the [application validation guidance](../SKILL.md). [Test frameworks](https://google.github.io/styleguide/rustguide.html#use-the-right-per-codebase-test-framework)

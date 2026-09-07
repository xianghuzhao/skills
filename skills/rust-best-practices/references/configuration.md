# Configuration

Read when adding startup settings, configuration files, environment variables, or CLI overrides.

## Sources and Validation

- Parse simple settings directly into a typed structure. Use `config` when merging multiple sources warrants it; do not introduce a configuration framework for a single value.
- Gather settings once at startup, validate them, and pass typed settings into application construction. Keep environment access and configuration parsing out of business logic.
- Prefer async reads for files or remote configuration. With `config`, asynchronously read file contents and use `File::from_str` as a parsing source, or use an appropriate async source. Parsing remains synchronous; using an async builder alone does not make a synchronous source non-blocking.
- Apply precedence from lowest to highest: defaults, configuration file, project environment variables, explicitly supplied CLI arguments. Load only the sources the application actually supports.
- Treat required missing files and malformed supplied configuration as errors. An optional file may be absent, but parsing errors should not silently fall back to defaults.
- Validate types and domain constraints before constructing clients or starting workers. Keep sensitive values out of errors and Debug logging.

## Environment Naming

- Use the actual project name in uppercase, replacing hyphens with underscores. Prefix and nested field separators are both `__`: `my-app` uses `MY_APP__DATABASE__URL` for `database.url`.
- The prefix identifies the application, not each internal workspace crate. Preserve an existing application's externally used naming scheme unless changing it is part of the task.
- Configure `Environment::with_prefix("MY_APP").prefix_separator("__").separator("__")`. Do not include trailing separators in the prefix itself.
- Parse numeric and boolean settings into their declared types. If lists are needed, specify their encoding deliberately instead of enabling list parsing for every field.
- CLI overrides must distinguish "not supplied" from a supplied value. Prefer `Option<T>` for override arguments, or check clap's value source. A parser's default value must not overwrite a value from the file or environment. Preserve explicit `false` and zero values for subsequent domain validation.

## Layering Example

This complete example demonstrates synchronous parsing of already acquired sources, not file I/O. It needs `config` with TOML support and `serde` with derive support. The optional CLI override models an explicitly supplied argument.

```rust
use config::{Config, ConfigError, Environment, File, FileFormat};
use serde::Deserialize;
use std::collections::HashMap;

#[derive(Debug, Deserialize, PartialEq)]
pub struct Settings {
    pub server: Server,
}

#[derive(Debug, Deserialize, PartialEq)]
pub struct Server {
    pub port: u16,
    pub enabled: bool,
}

pub fn load_settings(
    file_contents: &str,
    environment: HashMap<String, String>,
    cli_port: Option<u16>,
) -> Result<Settings, ConfigError> {
    let mut builder = Config::builder()
        .set_default("server.port", 8080)?
        .set_default("server.enabled", true)?
        .add_source(File::from_str(file_contents, FileFormat::Toml))
        .add_source(
            Environment::with_prefix("MY_APP")
                .prefix_separator("__")
                .separator("__")
                .try_parsing(true)
                .source(Some(environment)),
        );

    if let Some(port) = cli_port {
        builder = builder.set_override("server.port", port)?;
    }

    builder.build()?.try_deserialize()
}
```

In production, the default Environment source can read the process environment; the injected map above makes tests independent of it. Add the application's domain validation after deserialization. For example, whether port zero is allowed depends on whether an ephemeral listening port is acceptable.

## Tests

- Verify defaults and the complete precedence chain, including an omitted CLI override and an explicitly supplied value.
- Verify project prefix isolation, `__` nesting, and numeric/boolean conversion. Test invalid numeric values and domain constraints as errors.
- Inject environment maps and file contents into tests. Do not mutate process-global environment variables from parallel tests.

See [config Environment](https://docs.rs/config/latest/config/struct.Environment.html) and [clap value sources](https://docs.rs/clap/latest/clap/parser/enum.ValueSource.html).

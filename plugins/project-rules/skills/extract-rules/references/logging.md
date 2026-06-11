# Extracting Logging Conventions

## What to examine

Find the logging entry points (grep for `console.`, `logger`, `log.`, `logging.`, `slog`, `zap`, `tracing::`) and sample 10 to 15 call sites plus the logger setup code:

- **Library and setup**: which logging library; where the logger is configured (single setup module?); how modules obtain a logger (per-module `getLogger(__name__)`, injected logger, global singleton, `console` directly).
- **Structured vs formatted**: key-value/JSON structured logs vs interpolated message strings; if structured, which fields are conventional (`request_id`, `user_id`, `duration_ms`) and how they are spelled.
- **Level usage**: what actually gets logged at error, warn, info, debug; whether errors are logged where caught or where thrown; whether expected failures (validation, 404s) are logged at all and at what level.
- **Message style**: sentence case or lower case, present tense ("starting sync") vs past ("sync started"), error messages including the operation and identifiers.
- **Context propagation**: child loggers with bound context, correlation IDs through async flows, middleware that attaches request context.
- **Redaction and hygiene**: any masking of secrets, tokens, emails, or PII; fields that are deliberately never logged.
- **Console usage policy**: in projects with a logging library, where raw `console.log`/`print` still appears (scripts and CLIs may be exempt — note the scope).

## Convention vs accident

- Leftover debug `print`/`console.log` calls are accidents; do not let them dilute a clear logging-library convention. Flag them in the report if numerous.
- CLI tools writing user-facing output to stdout is output, not logging; do not conflate the two.
- If half the codebase logs structured and half logs strings, report the split and which direction recent code takes.

## What to report

Rules such as: the logging library and how to obtain a logger, required context fields and their exact names, level semantics, message style, the redaction policy, and where plain console/print output is acceptable. If the project has no observable logging convention, report that finding honestly rather than inventing one.

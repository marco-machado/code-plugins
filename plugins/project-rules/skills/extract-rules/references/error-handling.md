# Extracting Error Handling Conventions

## What to examine

Grep for the language's error vocabulary (`throw`, `catch`, `raise`, `except`, `if err != nil`, `Result`, `?`, `rescue`) and sample 10 to 15 call sites plus any error definition modules:

- **Error types**: custom error classes or types vs built-ins; a shared error hierarchy or per-module errors; error codes or enums; how custom errors carry context (fields, `cause`, wrapped inner errors).
- **Wrapping and propagation**: Go `fmt.Errorf` with `%w` vs sentinel errors; Rust `thiserror` for libraries vs `anyhow` for binaries; JavaScript `cause` option; Python `raise ... from`. Does code add context at each level or pass errors through untouched?
- **Boundaries**: where errors are actually caught — request middleware, job runners, CLI entry points, React error boundaries — versus where code lets them propagate. Most well-factored codebases catch in few places; map them.
- **Recovery vs reporting**: which errors are swallowed deliberately (with what justification), which are logged and continued, which abort the operation.
- **Result-style returns**: `Result`/`Either`/tuple returns instead of exceptions, and in which layers.
- **API error shape**: for services, the wire format of errors (problem+json, `{ error: { code, message } }`, gRPC statuses) and where it is produced.
- **Validation failures**: treated as errors or as values; which library produces them and how they reach the user.
- **Process-level handlers**: `unhandledRejection`/`uncaughtException` hooks, panic recovery, signal handling.

## Convention vs accident

- A bare `except:` or empty `catch` in one old file against a codebase of careful handling is an accident — or a bug worth flagging in the report — not a convention.
- Different layers legitimately differ: libraries returning errors while the application layer throws is itself the convention; report it scoped by layer.
- If both exceptions and result-returns are common in the same layer, report the split as an inconsistency.

## What to report

Rules such as: which error types to throw and where they are defined, required wrapping when crossing module boundaries, where catching is allowed, what must never be swallowed, the API error response shape, and how validation failures are surfaced. Include the boundary map (the few places that catch) since it tells future code where not to add handlers.

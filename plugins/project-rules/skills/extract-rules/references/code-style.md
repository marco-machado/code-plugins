# Extracting Code Style and Function Declaration Conventions

## Tooling check first

Read formatter and linter configs (`.prettierrc*`, `eslint.config.*`, `biome.json`, `.editorconfig`, `ruff.toml`, `pyproject.toml` tool sections, `rustfmt.toml`, `.clang-format`). Everything they enforce — indentation, quotes, semicolons, line length, trailing commas — is tool territory: report it as tool-enforced and move on. Extract only the style choices tools do not decide.

## What to examine

Sample 10 to 15 source files for the choices that remain after formatting:

### Function declarations

- Declaration form: `function` declarations vs arrow functions assigned to consts; named vs anonymous; method shorthand in objects and classes.
- Export style relative to declaration: `export function foo()` vs declare-then-export.
- Parameter patterns: options objects vs positional parameters, and at what arity the code switches; destructuring in the parameter list; default values.
- Return typing: explicit return type annotations vs inferred (TypeScript), `-> None` and full annotations vs partial (Python).
- Async style: async/await vs promise chains vs callbacks; sync vs async variants of APIs.

### Broader style

- Class-based vs functional organization, and where each is used.
- Early returns and guard clauses vs nested conditionals.
- Mutability habits: `const` vs `let`, frozen objects, dataclasses vs dicts, builder vs literal construction.
- Null handling: optional chaining, sentinel values, `Optional`/`Maybe` types, non-null assertions.
- Typing discipline: `any`/`unknown` usage, type assertions, generics appetite, `# type: ignore` frequency.
- Comment and docstring conventions: JSDoc/docstring presence and format, density of inline comments, TODO format (`TODO(name):`?).
- File internal ordering: exports first or last, helpers above or below their callers, one public symbol per file or several.

## Convention vs accident

- Style differences that track file age indicate migration; report the direction recent code takes.
- Differences that track authorship with no dominant pattern are an inconsistency to report, not a rule.
- A pattern that only appears in one module may be that module's local idiom; check at least three directories before calling it project-wide.

## What to report

Rules such as: the function declaration form for top-level functions vs callbacks, explicit return type policy, options-object threshold, async style, early-return preference, docstring format and when one is required, and typing strictness expectations. Note which dimensions are delegated to tools and to which config file.

# Extracting Import Conventions

## What to examine

Open 10 to 15 source files spread across different directories and ages, and inspect their import blocks for:

- **Grouping and ordering**: standard library, third-party, then internal? Blank lines between groups? Alphabetical within groups?
- **Path style**: relative imports (`../utils`) vs absolute from a root (`src/utils`) vs path aliases (`@/utils`, `~/lib`). Check `tsconfig.json` paths, `jsconfig.json`, webpack/vite aliases, or Python package layout to see which styles are even available, then which the code prefers.
- **Depth limits**: are deep relative chains (`../../..`) present or avoided?
- **Import form**: named vs default imports and exports; namespace imports (`import * as`); in Python, `import module` vs `from module import name`; in Go, dot imports or aliased imports.
- **Type-only imports**: `import type` in TypeScript, `TYPE_CHECKING` blocks in Python.
- **Barrel files / re-exports**: do directories expose an `index.ts` or `__init__.py` that re-exports, and do consumers import from the barrel or reach into modules directly?
- **Side-effect imports**: bare imports for polyfills, CSS, or registration, and where they appear (entry points only?).
- **Lazy or dynamic imports**: `import()`, `importlib`, conditional requires — when are they used?

## Tooling check first

Before attributing anything to convention, check for enforcement:

- `eslint-plugin-import` / `import/order`, `simple-import-sort`, `eslint-plugin-perfectionist`
- `isort` or Ruff's `I` rules in Python configs
- `goimports`/`gofmt` (always enforced in Go)
- Biome, dprint, or Prettier plugins with import sorting

Anything a tool enforces is reported as tool-enforced, not as a hand-followed rule.

## Convention vs accident

- Mixed relative and aliased imports across the codebase usually means a migration in progress — check file dates with `git log` to find the current direction.
- One file with unusual ordering among a dozen consistent files is an accident; report the dominant pattern.
- Different conventions in `src/` vs `tests/` vs `scripts/` can each be legitimate; report them scoped to their area rather than averaging them.

## What to report

Rules such as: import group order, alias usage and when relative imports are acceptable, barrel usage policy, named vs default export preference, type-only import usage, and where side-effect imports belong. For each, include the evidence ratio and confidence as defined in the task prompt.

# Extracting Naming Conventions

## What to examine

Naming splits into file-system naming and in-code naming; sample both.

### Files and directories

- File name casing per file kind: components (`UserCard.tsx` vs `user-card.tsx`), modules, tests, styles, configs. The casing often differs by kind — report per kind.
- Directory casing and pluralization (`utils` vs `util`, `components` vs `Component`).
- Suffix conventions: `.service.ts`, `.controller.ts`, `_test.go`, `.stories.tsx`, `.d.ts` — which suffixes exist and what they signal.
- Index/barrel naming and special files the project treats as magic.

### In code

- Casing per symbol kind, especially where the language leaves a choice: functions, variables, classes, types/interfaces (any `I` prefix or `T` prefix?), enums and their members, constants (`SCREAMING_SNAKE` for which kinds of constants — all, or only true compile-time ones?).
- Boolean naming: `is`/`has`/`should`/`can` prefixes, positive vs negative names (`isEnabled` vs `isDisabled`).
- Event and handler naming: `onClick` props vs `handleClick` implementations; emitter event string format.
- Acronym casing: `HttpClient` vs `HTTPClient`, `userId` vs `userID` — codebases are rarely accidental about this once one form dominates.
- Privacy markers: `_prefix`, `#private`, `__dunder`, or module-level privacy via exports.
- Domain vocabulary: the canonical names for core entities (is it `account`, `user`, or `customer`?) and abbreviations the project blesses (`ctx`, `req`, `res`, `cfg`) vs spells out.
- Wire and storage naming: JSON field casing in APIs, database column style, env var prefixes — and where conversion happens.

## How to sample

Use Glob output itself as evidence for file naming — list a few hundred file names and count casing per extension and directory. For in-code naming, grep declarations (`class `, `interface `, `def `, `const `, `func `) across 10 to 15 files.

## Convention vs accident

- File naming is high-signal: globs give near-complete counts, so report exact ratios.
- A single `IUser` interface among plain-named interfaces is an accident; a third of them is an inconsistency to report.
- Vocabulary conflicts (`user` in half the code, `account` in the other half for the same concept) are among the most valuable findings — report them prominently.

## What to report

Rules such as: file naming per file kind, symbol casing per symbol kind, boolean and handler patterns, acronym casing, blessed abbreviations, canonical domain terms, and wire-format naming with its conversion point.

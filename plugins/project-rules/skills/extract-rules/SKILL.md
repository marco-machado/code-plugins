---
name: extract-rules
description: This skill should be used when the user asks to "extract project rules", "extract coding conventions", "document codebase conventions", "generate coding rules from the codebase", "analyze project conventions", "update project rules", "refresh the rules from the codebase", or wants implicit codebase patterns (imports, framework usage, code style, error handling, logging, testing, platform compatibility, naming) turned into explicit rule files under .claude/rules/.
version: 0.1.0
argument-hint: "[imports | framework | code-style | error-handling | logging | testing | platform | naming — omit for all]"
allowed-tools: ["Task", "Read", "Write", "Edit", "Glob", "Grep", "Bash", "AskUserQuestion"]
---

# Extract Project Rules

Analyze the current repository to discover the conventions it actually follows and write them as explicit rule files under `.claude/rules/`, one file per category. Claude Code loads these files as project memory, so every extracted rule becomes active guidance for future sessions.

Every rule must be derived from observed evidence in this codebase — never from general best practices. A rule the codebase does not follow is worse than no rule.

## Input

```text
$ARGUMENTS
```

## Categories

| Category | Output file | Covers |
|----------|-------------|--------|
| imports | `.claude/rules/imports.md` | Import ordering, grouping, aliases, relative vs absolute, barrel files |
| framework | `.claude/rules/framework.md` | Framework-specific idioms, file roles, data flow, deviations from defaults |
| code-style | `.claude/rules/code-style.md` | Language style, function declaration patterns, typing discipline, comments |
| error-handling | `.claude/rules/error-handling.md` | Error types, wrapping, boundaries, propagation vs recovery |
| logging | `.claude/rules/logging.md` | Logging library, levels, structure, context fields, redaction |
| testing | `.claude/rules/testing.md` | Test framework, file placement, naming, structure, mocking, fixtures |
| platform | `.claude/rules/platform.md` | OS portability, runtime version constraints, shell usage, targets |
| naming | `.claude/rules/naming.md` | File, directory, variable, type, and constant naming patterns |

Argument aliases: `errors` or `exceptions` map to error-handling; `style` or `functions` to code-style; `tests` to testing; `logs` to logging; `compat` or `compatibility` to platform; `conventions` (alone) to all categories.

## Workflow

### 1. Parse arguments

Resolve `$ARGUMENTS` to a category list using the table and aliases above. When empty, select all eight categories. When an argument matches no category or alias, ask the user to pick from the valid categories via AskUserQuestion before proceeding.

### 2. Survey the project

Build a short project context block (10 lines maximum) that every subagent will receive:

- Languages in use and rough proportions (glob for file extensions, count matches)
- Package manifests found (`package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, `Gemfile`, `pom.xml`, `*.csproj`, etc.) and the frameworks and key dependencies they declare
- Formatter and linter configs present (`.prettierrc*`, `eslint.config.*`, `.editorconfig`, `ruff.toml`, `.golangci.yml`, etc.)
- Top-level directory layout (one line)
- Monorepo structure, if any

Skip dependency directories (`node_modules`, `vendor`, `.venv`, `dist`, `build`, `target`) in every search.

### 3. Read existing rules and project memory

For each selected category, check whether `.claude/rules/<category>.md` exists. Read existing files now — they are needed for the intelligent update in step 5.

Also read the project's memory files: the root `CLAUDE.md`, `CLAUDE.local.md` if present, and any nested `CLAUDE.md` files in subdirectories (skip dependency directories). Note which conventions they already state — these are needed for deduplication in step 5.

### 4. Launch one analysis subagent per category, in parallel

Launch all subagents in a single message using the Task tool with the Explore subagent type (read-only); fall back to the general-purpose type if Explore is unavailable. Use this prompt template for each, substituting the category, the reference path, and the project context from step 2:

```
Analyze this repository to discover its <category> conventions.

First, read ${CLAUDE_PLUGIN_ROOT}/skills/extract-rules/references/<category>.md and follow
its checklist: it defines what to examine, how to sample files, and how to tell a
convention from an accident.

Project context:
<project context block>

Skip node_modules, vendor, .venv, dist, build, target, and generated files.

Evidence standard:
- A convention requires at least 3 independent occurrences across at least 3 files,
  with a clear majority (roughly 80 percent or more) following the pattern.
- Sample breadth over depth: check a pattern across 10 to 15 files rather than
  reading 3 files exhaustively.
- When patterns conflict, recent code outweighs old code; use git log on disputed
  files to judge which pattern is current.
- Generated files, vendored code, and migration files are not evidence.

Return a list of candidate rules. For each rule report:
- Statement: one imperative sentence describing the convention
- Evidence: how many files examined, how many follow it (e.g. "12 of 13 files sampled")
- Confidence: high (near-universal), medium (dominant with exceptions), low (plurality only)
- Exceptions: where the codebase deviates and whether the deviation looks intentional
  (e.g. tests, scripts, legacy directories)

Also report patterns that are enforced by a formatter or linter config rather than by
hand, so they can be attributed to the tool instead of written as rules.

Report only what the code shows. Do not include best practices the codebase does not
actually follow. If the codebase is genuinely inconsistent on some dimension, say so
explicitly rather than inventing a rule.
```

### 5. Synthesize and write rule files

Read `${CLAUDE_PLUGIN_ROOT}/skills/extract-rules/references/output-format.md` for the rule file template and writing guidelines, then for each category:

1. Keep high-confidence rules as-is; keep medium-confidence rules with their exception noted inline; drop low-confidence rules (mention notable ones in the chat report instead).
2. Attribute tool-enforced patterns to the tool in a single line (e.g. "Import order is enforced by eslint-plugin-import; see eslint.config.js") rather than restating them as rules.
3. Skip rules already stated in a CLAUDE.md file (matched by meaning, not exact text) — they are already loaded as memory and duplicating them wastes context. When a finding contradicts a CLAUDE.md instruction, do not edit CLAUDE.md; surface the conflict in the final report and let the user resolve it.
4. When no existing file: write a fresh `.claude/rules/<category>.md` following the template.
5. When a file exists, update intelligently:
   - Keep existing rules that the new findings still support.
   - Keep existing rules the analysis neither confirmed nor contradicted — these are likely manual edits expressing intent; never delete them.
   - Replace rules the codebase now contradicts, and list each replacement in the final report so the user can veto it.
   - Add newly discovered rules.
6. Create the `.claude/rules/` directory if it does not exist.

### 6. Report

End with a summary the user can act on:

- A table: category, file written, rules added, rules updated, rules preserved
- Replaced rules, shown as before and after, for user review
- Conflicts between the findings and existing CLAUDE.md instructions, for the user to resolve
- Inconsistencies worth fixing: dimensions where the codebase splits between two patterns, with rough proportions, so the user can decide which to standardize on
- A reminder that the files in `.claude/rules/` load automatically as project memory and can be edited by hand

## Evidence standard

The evidence standard embedded in the subagent prompt template above also governs synthesis: when judging whether a finding supports, contradicts, or merely fails to confirm an existing rule, hold it to the same occurrence and majority bar before acting on it.

## Reference files

All paths are relative to `${CLAUDE_PLUGIN_ROOT}/skills/extract-rules/`. Per-category extraction checklists, loaded by the analysis subagents:

- **`references/imports.md`** — import patterns
- **`references/framework.md`** — framework conventions
- **`references/code-style.md`** — language style and function declarations
- **`references/error-handling.md`** — error handling
- **`references/logging.md`** — logging practices
- **`references/testing.md`** — testing practices
- **`references/platform.md`** — platform compatibility
- **`references/naming.md`** — naming conventions

Loaded by the main workflow:

- **`references/output-format.md`** — rule file template and writing guidelines

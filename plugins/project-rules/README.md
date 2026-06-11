# project-rules

Extract the conventions a codebase actually follows — import patterns, framework idioms, code style, error handling, logging, testing practices, platform compatibility, and naming — and write them as explicit rule files under `.claude/rules/`. Claude Code loads those files as project memory, so the extracted rules immediately guide future sessions.

Every rule is derived from observed evidence in the repository, never from generic best practices.

## Installation

```bash
/plugin marketplace add marco-machado/code-plugins
/plugin install project-rules@code-plugins
```

## Usage

```bash
# Analyze all categories
/project-rules:extract-rules

# Limit to specific categories
/project-rules:extract-rules testing naming
/project-rules:extract-rules errors imports
```

## Categories

| Category | Output file | Covers |
|----------|-------------|--------|
| imports | `.claude/rules/imports.md` | Ordering, grouping, aliases, relative vs absolute, barrel files |
| framework | `.claude/rules/framework.md` | Framework idioms, file roles, blessed data/state patterns |
| code-style | `.claude/rules/code-style.md` | Language style, function declarations, typing discipline |
| error-handling | `.claude/rules/error-handling.md` | Error types, wrapping, catch boundaries, API error shape |
| logging | `.claude/rules/logging.md` | Library, levels, structured fields, redaction |
| testing | `.claude/rules/testing.md` | Framework, placement, naming, structure, mocking, fixtures |
| platform | `.claude/rules/platform.md` | OS portability, runtime versions, shell dialect, targets |
| naming | `.claude/rules/naming.md` | File, symbol, boolean, acronym, and domain naming |

Aliases work too: `errors`, `style`, `functions`, `tests`, `logs`, `compat`.

## How it works

1. Surveys the project: languages, frameworks, formatter and linter configs, layout.
2. Launches one read-only analysis subagent per category, in parallel, each guided by a per-category extraction checklist.
3. Applies an evidence standard: a convention needs a clear majority (roughly 80 percent or more) across at least 3 files. Tool-enforced patterns are attributed to the tool instead of restated as rules. Genuinely inconsistent dimensions are reported as inconsistencies, not invented rules.
4. Writes one rule file per category and reports what was added, updated, and preserved.

## Re-runs and manual edits

Rule files are meant to be edited by hand. On re-runs, existing rules that are still supported (or simply not contradicted) are preserved verbatim; only rules the codebase now contradicts are replaced, and every replacement is listed in the report for review.

Conventions already stated in CLAUDE.md files (root, local, or nested) are never duplicated into rule files — they are already loaded as project memory. CLAUDE.md is never edited; if the codebase contradicts one of its instructions, the conflict is reported for you to resolve.

## Structure

```
project-rules/
├── .claude-plugin/plugin.json
└── skills/
    └── extract-rules/
        ├── SKILL.md                  # /project-rules:extract-rules workflow
        └── references/
            ├── imports.md            # Per-category extraction checklists
            ├── framework.md
            ├── code-style.md
            ├── error-handling.md
            ├── logging.md
            ├── testing.md
            ├── platform.md
            ├── naming.md
            └── output-format.md      # Rule file template and update semantics
```

This plugin uses the `skills/` format for its slash command (the current recommended layout) rather than the legacy `commands/` directory used by older plugins in this marketplace.

## License

MIT

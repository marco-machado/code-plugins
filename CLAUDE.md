# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A **multi-plugin marketplace repository** for Claude Code. Contains markdown-based plugins that extend Claude Code with slash commands, subagents, and auto-activating skills. No build system, no dependencies — all components are plain markdown files with YAML frontmatter.

**Marketplace:** `code-plugins` (GitHub: `marco-machado/code-plugins`)

## Repository Layout

```
code-plugins/
├── .claude-plugin/marketplace.json   # Plugin registry (all plugins listed here)
├── plugins/
│   ├── issue-workflow/               # GitHub Issues lifecycle management
│   │   ├── .claude-plugin/plugin.json
│   │   ├── commands/                 # Slash commands (/issue-workflow:create, etc.)
│   │   ├── agents/                   # Subagents (issue-triage)
│   │   ├── skills/                   # Auto-activating knowledge
│   │   └── templates/                # Files copied to target repos by /setup
│   └── ui-critic/                    # UI screenshot design analysis
│       ├── .claude-plugin/plugin.json
│       ├── commands/                 # /ui-critic:analyze
│       └── skills/                   # Design principles knowledge
```

## Plugin Component Anatomy

**Commands** (`commands/*.md`): Slash commands. Frontmatter: `description`, `argument-hint`, `allowed-tools`. Body is the step-by-step prompt.

**Agents** (`agents/*.md`): Subagents. Frontmatter: `name`, `description` (with trigger examples), `tools`, `model`, `color`. Body is the system prompt.

**Skills** (`skills/*/SKILL.md`): Auto-activating knowledge. Frontmatter: `name`, `description` (with trigger keywords), `version`. Body is the methodology/reference content.

**Templates** (`templates/`): Static files commands copy into target repositories.

## Git Conventions

- **Conventional Commits** — `feat:`, `fix:`, `refactor:`, `docs:`, `chore:`, etc. Scope is optional (e.g., `feat(issue-workflow): add close command`)
- **Conventional Branches** — `feat/`, `fix/`, `refactor/`, `docs/`, `chore/`, etc. (e.g., `feat/ui-critic-plugin`, `fix/label-fallback`)

## Key Conventions

- **No build step** — edit markdown, test directly
- **`$ARGUMENTS`** — placeholder for user input in commands
- **`${CLAUDE_PLUGIN_ROOT}`** — resolves to plugin root for referencing bundled files (templates, etc.)
- **`allowed-tools` minimalism** — only list tools the command actually uses
- **Repo discovery** — always use `gh repo view` dynamically, never hardcode owner/repo
- **User decisions** — use `AskUserQuestion` tool, not inline text prompts
- **State files** — plugins can create `{plugin-name}.local.md` for per-project state (gitignored via `*.local.md`)

## Registering a New Plugin

1. Create `plugins/{name}/` with `.claude-plugin/plugin.json` manifest
2. Add commands/agents/skills as needed
3. Add the plugin entry to `.claude-plugin/marketplace.json` (name, source path, description, version, keywords, category)

## Testing Plugins Locally

```bash
claude --plugin-dir /Users/machado/Projects/code-plugins/plugins/{plugin-name}
```

## Installing from Marketplace

```bash
/plugin marketplace add marco-machado/code-plugins
/plugin install issue-workflow@code-plugins
```

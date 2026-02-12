# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A Claude Code plugin (`issue-workflow`) that provides an opinionated GitHub Issues lifecycle management workflow. It lives inside a multi-plugin marketplace repo (`code-plugins`) at `plugins/issue-workflow/`.

## Repository Structure

```
code-plugins/                        # Marketplace root (git root)
├── .claude-plugin/marketplace.json  # Marketplace manifest
├── plugins/
│   └── issue-workflow/              # This plugin (CWD)
│       ├── .claude-plugin/plugin.json
│       ├── commands/                # Slash commands (markdown with YAML frontmatter)
│       │   ├── create.md            # /issue-workflow:create
│       │   ├── implement.md         # /issue-workflow:implement
│       │   ├── close.md             # /issue-workflow:close
│       │   ├── setup.md             # /issue-workflow:setup
│       │   └── triage.md            # /issue-workflow:triage
│       ├── agents/
│       │   └── issue-triage.md      # Subagent for backlog health analysis
│       ├── skills/
│       │   └── issue-workflow/SKILL.md  # Auto-activating skill (methodology reference)
│       └── templates/               # Bundled files copied to target repos by /setup
│           ├── issue-templates/     # GitHub issue form templates (.yml)
│           └── workflows/stale.yml  # GitHub Actions stale issue automation
```

## Plugin Architecture

All plugin components are **plain markdown files** with YAML frontmatter — no build step, no dependencies.

- **Commands** (`commands/*.md`): User-invocable slash commands. Frontmatter defines `description`, `argument-hint`, and `allowed-tools`. The body is a prompt that Claude follows step-by-step.
- **Agents** (`agents/*.md`): Subagent definitions. Frontmatter includes `tools`, `model`, and `color`. Body is the system prompt.
- **Skills** (`skills/*/SKILL.md`): Knowledge that auto-activates based on conversation topic. Frontmatter `description` contains trigger conditions.
- **Templates** (`templates/`): Static files the `/setup` command copies into target repositories (issue templates, GitHub Actions workflows).

## Key Conventions

- Commands use `$ARGUMENTS` placeholder for user input and `${CLAUDE_PLUGIN_ROOT}` to reference plugin-bundled files.
- The workflow enforces exactly **3 labels** (`blocked`, `stale`, `pinned`) and **3 issue types** (Task, Bug, Feature).
- Branch naming: `issue/{NUMBER}-{short-description}`.
- Issue body structure: Problem → Approach → Acceptance Criteria → Tasks → Notes.
- All GitHub API interaction uses the `gh` CLI (not raw REST calls).
- The `.gitignore` excludes `*.local.md` files (per-project plugin state).

## When Modifying Commands

Each command follows a consistent pattern: discover repo → validate input → perform action → report result. When adding or modifying commands:

1. Keep `allowed-tools` minimal — only list tools the command actually needs
2. Use `AskUserQuestion` for user decisions, not inline text prompts
3. Use `gh api` for operations not covered by `gh issue`/`gh label` subcommands
4. Always discover the repo dynamically (`gh repo view`) — never hardcode owner/repo

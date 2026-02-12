# issue-workflow

An opinionated Claude Code plugin for managing GitHub Issues through their full lifecycle — create, implement, triage, and close — with baked-in conventions for solo developers and small teams.

## Philosophy

Issues exist to track work, not to create work. This plugin codifies a lightweight workflow:

- **Structured issues** with Problem/Approach/Acceptance Criteria/Tasks format
- **Three issue types** (Task, Bug, Feature) instead of label sprawl
- **Four labels only** (`blocked`, `docs-sync-required`, `stale`, `pinned`)
- **`issue/{N}-description` branch naming** for traceability
- **Zombie detection** via automated stale workflow
- **Right-sized issues** with checklists, not atomic sub-issues

## Commands

| Command | Description |
|---------|-------------|
| `/issue-workflow:setup` | Check repo configuration and propose fixes (labels, types, templates, stale workflow) |
| `/issue-workflow:create` | Create a well-structured issue with proper type and body format |
| `/issue-workflow:implement` | Fetch an issue, analyze the codebase, create a branch, and generate an implementation plan |
| `/issue-workflow:triage` | Review open issues — find stale/zombie/blocked items and suggest actions |
| `/issue-workflow:close` | Close an issue with workflow checks (docs-sync, checklist completion) |

## Skill

The **issue-workflow** skill activates automatically when you discuss work planning, branch naming, issue management, or backlog health in conversation.

## Agent

The **issue-triage** agent can be triggered proactively to analyze backlog health and produce a structured triage report.

## Installation

### Local testing

```bash
claude --plugin-dir /path/to/issue-workflow
```

### Project installation

Copy the plugin directory into your project or symlink it.

## Requirements

- `gh` CLI installed and authenticated (`gh auth status`)
- Git repository with a GitHub remote
- For issue types: organization-level issue types configured (Task, Bug, Feature)

## First-Time Setup

Run `/issue-workflow:setup` in your repo to verify configuration and create any missing labels, templates, or workflows.

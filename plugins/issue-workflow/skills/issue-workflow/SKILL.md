---
name: issue-workflow
description: |
  This skill should be used when the conversation involves work planning, issue management, issue creation, branch naming conventions, backlog health, issue triage, or GitHub Issues workflow. It provides the opinionated methodology for managing GitHub Issues as a solo developer or small team.

  Triggers on topics like: "create an issue", "what branch name", "triage my backlog", "close this issue", "issue template", "stale issues", "zombie issues", "blocked issues", "how should I structure this issue", "issue workflow", "track this work", "what labels should I use", "how to close an issue", "PR closing keywords".
version: 0.1.0
---

# GitHub Issues Workflow Methodology

An opinionated, lightweight issue tracking methodology for solo developers and small teams that maximizes value while minimizing overhead.

## Core Philosophy

Issues exist to track work, not to create work. If maintaining your issue tracker takes more than 5 minutes per week, you're doing it wrong.

## Issue Types

GitHub Issue Types classify work at the organization level:

| Type | When to Use | Example |
|------|-------------|---------|
| **Task** | Technical work, refactors, maintenance | "Refactor error handling pattern" |
| **Bug** | Something is broken | "Race condition in auto-completion" |
| **Feature** | New functionality | "Allow unlimited paused stints" |

Types are set at issue creation via the GitHub API (the `create` command handles this automatically).

**Personal repos:** Native Issue Types are an org-only GitHub feature. On personal repos, the workflow uses `type:task`, `type:bug`, and `type:feature` labels as a fallback. The `/setup` command detects this automatically and creates the labels.

## Issue Sizing

Issues represent a logical chunk of work, not atomic tasks. Group related work with checklists:

**Too granular:** 3 separate issues for 3 empty states
**Right-sized:** 1 issue "Dashboard polish — empty states & error handling" with a checklist

Benefits: fewer issues, related work stays together, one PR closes everything.

## Issue Structure

Every issue follows this body format:

```markdown
## Problem
[What's broken or missing — the "why"]

## Approach
[How to solve it, or "TBD" if undecided]

## Acceptance Criteria
- [ ] Specific, testable condition
- [ ] Another verifiable outcome

## Tasks
- [ ] Sub-task 1
- [ ] Sub-task 2

## Notes
[Links, constraints, context — omit if none]
```

**Title convention:** `{Area}: {Clear, actionable description}`
- "Dashboard: Add empty states for no-data scenarios"
- "Auth: Handle expired sessions gracefully"

## Labels

Three workflow labels — required on all repos:

| Label | Purpose |
|-------|---------|
| `blocked` | Can't proceed, waiting on something |
| `stale` | Auto-applied by zombie detection |
| `pinned` | Exempt from stale detection |

**Personal repos** also use three type labels (`type:task`, `type:bug`, `type:feature`) since native Issue Types are unavailable.

Do NOT use labels for categorization (that's what issue types are for on org repos), priority (you already know what's urgent), or assignment (you're solo).

## Branch Naming

```
issue/{NUMBER}-{short-description}
```

Rules: always include issue number, lowercase, hyphens, keep it short. The `issue/` prefix is universal — the issue itself classifies work as Bug/Feature/Task.

Examples:
- `issue/42-dashboard-empty-states`
- `issue/15-auth-session-expiry`

## Closing Issues

Two methods:
1. **Via PR:** Include `Closes #XX` or `Fixes #XX` in PR description (auto-closes on merge)
2. **Manual:** `gh issue close {N} --comment "reason" --reason completed`

Before closing, check:
- All checklist items completed or explicitly deferred

## Zombie Issue Management

Issues inactive 30+ days are zombies. An automated stale workflow handles this:
- **Day 30:** Issue labeled `stale`, comment posted
- **Day 44:** Issue auto-closed if no activity

Issues with `blocked` or `pinned` labels are exempt.

**Triage questions:**
1. "Would I mass-close this if I had 100 issues?" → Close it
2. "If this disappeared, would I recreate it?" → No? Close it
3. "Can I describe the next concrete step?" → No? Rescope or close

## Anti-Patterns to Avoid

- **Issue inflation** — creating issues "just in case" (close if untouched for 3 months)
- **Label obsession** — if you have more than 3 workflow labels (6 on personal repos counting type labels), delete the extras
- **Roadmap duplication** — maintaining status in both roadmap doc AND issues
- **Future hoarding** — creating issues for work months away (put it in the roadmap doc instead)
- **Zombie tolerance** — letting old issues sit open indefinitely

## When NOT to Create Issues

- Sub-tasks (use checklists in a parent issue)
- Work you'll do in the next hour
- Future phases you won't touch for months
- Things already completed

## Plugin Commands

This plugin provides commands for the full issue lifecycle:

| Command | Purpose |
|---------|---------|
| `/issue-workflow:setup` | Check repo config (labels, types, templates) and fix gaps |
| `/issue-workflow:create` | Create a well-structured issue with proper type and body |
| `/issue-workflow:implement` | Fetch issue, analyze codebase, create branch, plan implementation |
| `/issue-workflow:triage` | Review open issues, find stale/zombie/blocked, suggest actions |
| `/issue-workflow:close` | Close issue with workflow checks (checklist completion) |

The **issue-triage** agent can also be triggered proactively to analyze backlog health.

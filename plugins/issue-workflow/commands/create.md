---
description: Create a GitHub issue following the issue workflow (proper type, structured body, area-prefixed title)
argument-hint: Description of the issue to create
allowed-tools: ["Bash", "Read", "AskUserQuestion"]
---

# Create Issue

Create a well-structured GitHub issue with the correct type, a clear title, and a structured body.

## User Input

```text
$ARGUMENTS
```

## Instructions

### 1. Discover Repository

```bash
gh repo view --json owner,name --jq '"\(.owner.login)/\(.name)"'
```

Store for the API call. If this fails, tell the user they need to be in a GitHub-connected repo.

### 2. Detect Repo Type

Read the `issue-workflow.local.md` state file from the project root. Parse the YAML frontmatter for `repo_type` (`personal` or `organization`).

If the state file doesn't exist, detect on-the-fly:

```bash
gh api users/{OWNER} --jq '.type'
```

This returns `"User"` (personal) or `"Organization"`. Warn the user to run `/issue-workflow:setup` to persist this detection.

### 3. Parse User Input

If arguments were provided, extract:
- Issue description/title
- Any mentioned type (bug, feature, task)
- Any specific details about the problem

If no arguments or insufficient detail, ask the user what needs to be done.

### 4. Determine Issue Type

| Type | When to Use |
|------|-------------|
| **Task** | Technical work, refactors, maintenance, infrastructure |
| **Bug** | Something is broken or not working as expected |
| **Feature** | New functionality or enhancement |

Infer from context if not explicitly stated:
- "fix", "broken", "error", "not working", "crash", "regression" → Bug
- "add", "implement", "new", "create", "allow", "enable" → Feature
- "refactor", "update", "remove", "clean up", "improve", "migrate" → Task

If ambiguous, ask the user.

### 5. Craft Issue Title

Follow the convention: `{Area}: {Clear, actionable description}`

The area should identify the affected part of the system (e.g., "Dashboard", "Auth", "API", "Database", "Timer", "Settings").

**Good titles:**
- "Dashboard: Add empty states for no-data scenarios"
- "Auth: Handle expired sessions gracefully"
- "Timer: Fix drift accumulation on long stints"

**Bad titles:**
- "Fix the thing"
- "Improvements"
- "Bug"

### 6. Craft Issue Body

Use this exact structure:

```markdown
## Problem

[What's broken, missing, or needs improvement — the "why"]

## Approach

[How to solve it, or "TBD" if undecided]

## Acceptance Criteria

- [ ] Criterion 1: [Specific, testable condition]
- [ ] Criterion 2: [Another verifiable outcome]

## Tasks

- [ ] Sub-task 1
- [ ] Sub-task 2

## Notes

[Relevant links, constraints, context — or omit section if none]
```

**Guidelines:**
- Problem section: concise but with enough context for future-you
- Approach: can be "TBD" if implementation is unclear
- Acceptance criteria: must be testable/verifiable
- Tasks: use checkboxes for progress tracking
- Notes: omit entirely if nothing relevant
- Right-size the issue — group related work with checklists rather than creating atomic issues

### 7. Create Issue via API

The API call depends on repo type:

**Organization repos** — use native issue types:

```bash
gh api repos/{OWNER}/{REPO}/issues -X POST \
  -f title="Issue title" \
  -f body="$(cat <<'EOF'
Issue body here
EOF
)" \
  -f type="Task|Bug|Feature"
```

**Personal repos** — omit `type`, apply `type:*` label instead:

```bash
gh api repos/{OWNER}/{REPO}/issues -X POST \
  -f title="Issue title" \
  -f body="$(cat <<'EOF'
Issue body here
EOF
)" \
  -f 'labels[]=type:task'
```

Use the matching label: `type:task`, `type:bug`, or `type:feature`.

### 8. Report Result

Output:
- Issue URL (construct from `https://github.com/{OWNER}/{REPO}/issues/{NUMBER}`)
- Issue number
- Assigned type (and whether it was set via native type or label)
- Brief confirmation of what was created

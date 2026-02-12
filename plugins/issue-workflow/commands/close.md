---
description: Close a GitHub issue with proper workflow checks (docs-sync, checklist completion, close reason)
argument-hint: "Issue number (e.g., 42)"
allowed-tools: ["Bash", "AskUserQuestion"]
---

# Close Issue

Close a GitHub issue following the workflow's closing checklist: verify documentation sync requirements, check task completion, and close with an appropriate reason.

## User Input

```text
$ARGUMENTS
```

## Instructions

### 1. Discover Repository & Fetch Issue

```bash
gh repo view --json owner,name --jq '"\(.owner.login)/\(.name)"'

gh issue view {NUMBER} --json number,title,body,labels,state
```

If the issue is already closed, tell the user and stop.

### 2. Pre-Close Checks

Run these checks before closing:

#### 2.1 Documentation Sync

Check if the issue has the `docs-sync-required` label.

If yes, warn the user:
> This issue has the `docs-sync-required` label. Before closing, ensure the referenced documentation has been updated. The issue is not complete until docs are synced.

Ask if documentation has been updated. If not, ask if they want to proceed anyway or handle docs first.

#### 2.2 Checklist Completion

Parse the issue body for checklist items (`- [ ]` and `- [x]`).

Count completed vs total items. If there are unchecked items, warn the user:
> This issue has {unchecked}/{total} checklist items still unchecked:
> - [ ] Item 1
> - [ ] Item 2

Ask if they want to:
- Close anyway (items were deferred or no longer relevant)
- Go back and complete the items first

#### 2.3 Blocked Label

Check if the issue has the `blocked` label. If yes, note that the issue was blocked and ask if the blocker was resolved.

### 3. Determine Close Reason

Ask the user for the close reason using AskUserQuestion:

| Reason | When | State Reason |
|--------|------|-------------|
| **Completed** | Work is done, all criteria met | `completed` |
| **Not planned** | Won't do, no longer relevant, duplicate | `not_planned` |
| **Deferred** | Valid but not now — will revisit later | `not_planned` |

### 4. Close the Issue

Based on the reason, close with an appropriate comment:

**Completed:**
```bash
gh issue close {NUMBER} --comment "Completed." --reason completed
```

**Not planned:**
```bash
gh issue close {NUMBER} --comment "{user-provided reason or 'Closing: no longer relevant'}" --reason "not planned"
```

**Deferred:**
```bash
gh issue close {NUMBER} --comment "Deferring — will revisit later if needed. Closed issues are searchable." --reason "not planned"
```

If the issue has the `blocked` label, also remove it:
```bash
gh issue edit {NUMBER} --remove-label "blocked"
```

### 5. Report

Output:
- Confirmation that issue was closed
- The close reason
- Any warnings that were acknowledged (unchecked items, docs not synced)
- Reminder: closed issues are searchable — if it becomes relevant again, reopen it

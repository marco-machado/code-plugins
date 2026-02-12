---
description: Review open GitHub issues — find stale/zombie/blocked items, categorize by health, and suggest triage actions
argument-hint: "Optional: --interactive for one-by-one triage"
allowed-tools: ["Bash", "Read", "AskUserQuestion"]
---

# Triage Issues

Review all open issues in the repository, categorize them by health, and suggest triage actions. Supports two modes: summary report (default) and interactive one-by-one triage.

## User Input

```text
$ARGUMENTS
```

## Instructions

### 1. Discover Repository

```bash
gh repo view --json owner,name --jq '"\(.owner.login)/\(.name)"'
```

### 2. Detect Repo Type

Read the `issue-workflow.local.md` state file from the project root. Parse the YAML frontmatter for `repo_type` (`personal` or `organization`).

If the state file doesn't exist, detect on-the-fly:

```bash
gh api users/{OWNER} --jq '.type'
```

This returns `"User"` (personal) or `"Organization"`. Warn the user to run `/issue-workflow:setup` to persist this detection.

### 3. Determine Mode

Check if `$ARGUMENTS` contains `--interactive` or `-i`.
- If yes → Interactive mode (Step 6)
- If no → Summary mode (Step 4-5)

### 4. Fetch All Open Issues (Summary Mode)

The jq query depends on repo type:

**Organization repos** — use native issue types:

```bash
gh api repos/{OWNER}/{REPO}/issues \
  --paginate \
  --jq '.[] | select(.pull_request == null) | {
    number: .number,
    title: .title,
    type: (.type.name // "none"),
    labels: [.labels[].name],
    updated_at: .updated_at,
    created_at: .created_at
  }'
```

**Personal repos** — extract type from `type:*` labels:

```bash
gh api repos/{OWNER}/{REPO}/issues \
  --paginate \
  --jq '.[] | select(.pull_request == null) | {
    number: .number,
    title: .title,
    type: (([.labels[].name | select(startswith("type:"))] | first // "none") | sub("^type:"; "")),
    labels: [.labels[].name | select(startswith("type:") | not)],
    updated_at: .updated_at,
    created_at: .created_at
  }'
```

Note: for personal repos, the `labels` array excludes `type:*` labels since those are reported in the `type` field.

### 5. Categorize & Present Report (Summary Mode)

Categorize each issue by health status using these rules:

| Category | Criteria | Recommended Action |
|----------|----------|--------------------|
| **Active** | Updated within 14 days | No action needed |
| **Aging** | Updated 14-30 days ago | Review — still relevant? |
| **Stale** | Updated 30-44 days ago, or has `stale` label | Triage: close, rescope, or comment |
| **Zombie** | Updated 44+ days ago | Strong candidate for closing |
| **Blocked** | Has `blocked` label | Check if blocker is resolved |

Present a summary report:

```
## Issue Triage Report: {owner}/{repo}

**Total open:** {count} | **Active:** {count} | **Needs attention:** {count}

### Needs Attention

| # | Title | Type | Status | Days Idle | Suggested Action |
|---|-------|------|--------|-----------|------------------|
| 42 | Dashboard polish | Feature | Stale | 35 | Close or rescope |
| 15 | Auth session fix | Bug | Zombie | 50 | Close — likely outdated |
| 7 | Refactor errors | Task | Blocked | 20 | Check if blocker resolved |

### Healthy Issues

| # | Title | Type | Last Updated |
|---|-------|------|-------------|
| 88 | Add analytics | Feature | 3 days ago |
| 90 | Fix timer drift | Bug | 1 day ago |
```

After the report, offer to take bulk actions:
- Close all zombies
- Remove `blocked` label from issues whose blockers are resolved
- Comment on stale issues to reset the timer

### 6. Interactive Triage Mode

If `--interactive` was specified, go through issues one by one, starting with the most stale.

For each issue:

1. Show issue details:
   ```
   ### #{number}: {title}
   **Type:** {type} | **Labels:** {labels} | **Last updated:** {relative_time}

   {first 200 chars of body}
   ```

2. Ask the user what to do using AskUserQuestion with these options:
   - **Close** — No longer relevant
   - **Defer** — Close with "Deferring to later"
   - **Rescope** — Edit to make more concrete (opens issue for editing)
   - **Keep** — Still relevant, add a comment to reset stale timer
   - **Skip** — Move to next issue without action

3. Execute the chosen action:
   - **Close:** `gh issue close {N} --comment "Closing: no longer relevant"`
   - **Defer:** `gh issue close {N} --comment "Deferring — will revisit later if needed"`
   - **Rescope:** Show the issue URL and suggest the user edit it. Optionally help rewrite the issue body.
   - **Keep:** `gh issue comment {N} --body "Still relevant — reviewed during triage"`
   - **Skip:** Continue to next issue

4. After all issues are triaged, show a summary of actions taken.

### 7. Triage Principles

Apply these guiding questions when making recommendations:

1. "Would I mass-close this if I had 100 issues?" → Recommend closing
2. "If this disappeared, would I recreate it?" → No? Recommend closing
3. "Can I describe the next concrete step?" → No? Recommend rescoping or closing
4. Issue open 3+ months with no progress → Recommend closing — it's not happening

An issue without a clear next action is not an issue, it's a wish.

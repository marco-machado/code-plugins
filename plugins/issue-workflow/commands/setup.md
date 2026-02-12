---
description: Check repository GitHub configuration against issue workflow requirements and propose fixes
allowed-tools: ["Bash", "Read", "Glob", "Write", "AskUserQuestion"]
---

# Repository Setup Check

Verify that the current repository's GitHub configuration matches the issue workflow requirements. Check labels, issue types, templates, and automation, then offer to fix what's missing.

## Instructions

### 1. Discover Repository

```bash
gh repo view --json owner,name --jq '"\(.owner.login)/\(.name)"'
```

Store the owner and repo name for subsequent API calls. If this fails, the user isn't in a GitHub-connected repo — tell them and stop.

Then detect the owner type:

```bash
gh api users/{OWNER} --jq '.type'
```

This returns `"User"` for personal accounts or `"Organization"` for org accounts. Store as `OWNER_TYPE`. This determines whether the repo can use native GitHub Issue Types (org-only) or needs `type:*` label fallback (personal repos).

### 2. Run All Checks

Run each check below. Collect results into a report card.

#### 2.1 Required Labels

Check for these three workflow labels:

| Label | Description | Color |
|-------|-------------|-------|
| `blocked` | Can't proceed, waiting on something | `d73a4a` |
| `stale` | No recent activity (auto-managed) | `ededed` |
| `pinned` | Exempt from stale detection | `006b75` |

```bash
gh label list --json name,description,color
```

Mark as PASS if all 3 exist, WARN if some missing, FAIL if none exist.

#### 2.2 Excess Labels

Flag labels that duplicate issue type functionality or add no value for solo/small-team development:
- `bug`, `enhancement`, `help wanted`, `good first issue`, `question`, `wontfix`, `duplicate`, `invalid`, `docs-sync-required`
- Any `priority-*` labels

These are excess because issue types (Bug/Feature/Task) handle categorization, and priority labels add overhead without value for solo developers.

Mark as PASS if no excess labels found, WARN if excess labels exist.

#### 2.3 Issue Types / Type Labels

This check branches based on `OWNER_TYPE`:

**If Organization:**

Check if the organization has issue types configured:

```bash
gh api orgs/{OWNER}/issue-types --jq '.[] | "\(.name)"'
```

Look for: Task, Bug, Feature.

Mark as PASS if all 3 exist, WARN if some missing. Note: this is an org-level setting and cannot be auto-fixed from the repo.

If the API returns an error (404 or permission denied), mark as WARN and note that issue types may not be enabled for this organization.

**If User (personal repo):**

Personal repos cannot use native GitHub Issue Types — they are an org-only feature. Instead, check for `type:*` labels from the label list already fetched in 2.1:

| Label | Description | Color |
|-------|-------------|-------|
| `type:task` | Task — technical work, refactors, maintenance | `0e8a16` |
| `type:bug` | Bug — something is broken | `d73a4a` |
| `type:feature` | Feature — new functionality or enhancement | `a2eeef` |

Mark as PASS if all 3 exist, WARN if some missing, FAIL if none exist.

#### 2.4 Issue Templates

Check for these files in `.github/ISSUE_TEMPLATE/`:
- `bug.yml`
- `feature.yml`
- `task.yml`
- `config.yml`

Use Glob to check the local filesystem. Mark as PASS if all exist, WARN if some missing, FAIL if directory doesn't exist.

#### 2.5 Stale Workflow

Check for `.github/workflows/stale.yml` using Glob.

Mark as PASS if exists, FAIL if missing.

### 3. Present Report Card

Output a formatted report. The issue type row adapts based on `OWNER_TYPE`:

**For Organization repos:**

```
## Repository Setup Report: {owner}/{repo}

| Check              | Status | Details                    |
|--------------------|--------|----------------------------|
| Required labels    | ✅/⚠️/❌ | {which are missing}      |
| Excess labels      | ✅/⚠️    | {which to consider removing} |
| Issue types (org)  | ✅/⚠️    | {which types available}   |
| Issue templates    | ✅/⚠️/❌ | {which are missing}      |
| Template config    | ✅/❌    | config.yml present?       |
| Stale workflow     | ✅/❌    | stale.yml present?        |
```

**For Personal repos:**

```
## Repository Setup Report: {owner}/{repo}

| Check              | Status | Details                    |
|--------------------|--------|----------------------------|
| Required labels    | ✅/⚠️/❌ | {which are missing}      |
| Excess labels      | ✅/⚠️    | {which to consider removing} |
| Type labels        | ✅/⚠️/❌ | {which type:* labels missing} |
| Issue templates    | ✅/⚠️/❌ | {which are missing}      |
| Template config    | ✅/❌    | config.yml present?       |
| Stale workflow     | ✅/❌    | stale.yml present?        |
```

### 4. Offer Fixes

For each non-passing check, offer to fix it. Use AskUserQuestion to confirm before making changes.

**Labels — create missing workflow labels:**
```bash
gh label create "blocked" --description "Can't proceed, waiting on something" --color "d73a4a"
gh label create "stale" --description "No recent activity (auto-managed)" --color "ededed"
gh label create "pinned" --description "Exempt from stale detection" --color "006b75"
```

**Labels — remove excess ones (ask first, list each one):**
```bash
gh label delete "{label}" --yes
```

**Issue types (Organization only) — cannot auto-fix.** Provide manual instructions:
> Issue types are configured at the organization level.
> Go to: https://github.com/organizations/{OWNER}/settings/issue-types
> Add types: Task, Bug, Feature

**Type labels (Personal repos only) — create missing ones:**
```bash
gh label create "type:task" --description "Task — technical work, refactors, maintenance" --color "0e8a16"
gh label create "type:bug" --description "Bug — something is broken" --color "d73a4a"
gh label create "type:feature" --description "Feature — new functionality or enhancement" --color "a2eeef"
```

**Issue templates — copy from plugin bundle:**
Create `.github/ISSUE_TEMPLATE/` directory if needed, then copy each missing template from `${CLAUDE_PLUGIN_ROOT}/templates/issue-templates/` using the Read tool to read the template source, then Write tool to create it in the repo.

**Template config — copy from plugin bundle:**
Same approach — read from `${CLAUDE_PLUGIN_ROOT}/templates/issue-templates/config.yml`, write to `.github/ISSUE_TEMPLATE/config.yml`.

**Stale workflow — copy from plugin bundle:**
Create `.github/workflows/` if needed, then copy from `${CLAUDE_PLUGIN_ROOT}/templates/workflows/stale.yml`.

### 5. Write State File

After all fixes are applied, write the `issue-workflow.local.md` state file at the target project root:

```markdown
---
repo_type: personal
owner: {OWNER}
repo: {REPO}
---
```

Set `repo_type` to `personal` if `OWNER_TYPE` is `"User"`, or `organization` if `OWNER_TYPE` is `"Organization"`.

Then check if `*.local.md` is in the project's `.gitignore`. If not, suggest adding it:

```bash
echo "*.local.md" >> .gitignore
```

Use AskUserQuestion to confirm before modifying `.gitignore`.

### 6. Summary

After applying fixes, briefly summarize what was changed including the state file creation. If everything passed from the start, congratulate the user — their repo is workflow-ready.

Mention:
- State file written at `issue-workflow.local.md` with `repo_type: {personal|organization}`
- Whether `.gitignore` was updated
- Any labels/templates/workflows created

---
description: Fetch a GitHub issue, analyze requirements against the codebase, and create an implementation plan with branch
argument-hint: "Issue number (e.g., 42)"
allowed-tools: ["Bash", "Read", "Glob", "Grep", "AskUserQuestion", "EnterPlanMode"]
---

# Implement Issue

Fetch a GitHub issue by number, analyze its requirements against the current codebase, ask clarifying questions when needed, and produce a detailed implementation plan including branch creation.

## User Input

```text
$ARGUMENTS
```

## Instructions

### 1. Discover Repository & Fetch Issue

```bash
# Get repo info
gh repo view --json owner,name --jq '"\(.owner.login)/\(.name)"'

# Fetch issue details
gh issue view {NUMBER} --json number,title,body,labels,state

# Fetch comments for additional context
gh api repos/{OWNER}/{REPO}/issues/{NUMBER}/comments --jq '.[].body'
```

**Output summary:**
- Issue number and title
- Current state (open/closed)
- Labels (especially `blocked`, `docs-sync-required`)

If the issue is closed, warn the user and ask if they want to proceed anyway.

### 2. Analyze Issue Requirements

Parse the issue body following the standard structure:

| Section | Extract |
|---------|---------|
| **Problem** | The core problem to solve (the "why") |
| **Approach** | Proposed solution (may be "TBD") |
| **Acceptance Criteria** | Checklist of testable conditions |
| **Tasks** | Sub-tasks already identified |
| **Notes** | Constraints, links, context |

**Identify:**
1. **Scope boundaries** — What's explicitly in/out of scope
2. **Unknowns** — Areas marked "TBD" or lacking detail
3. **Dependencies** — External factors or blocked items
4. **Documentation flags** — If `docs-sync-required` label exists, note which docs need updating

### 3. Codebase Analysis

Based on the issue requirements, analyze the relevant parts of the codebase. Discover the project structure dynamically — do NOT assume any specific framework or directory layout.

**Discovery steps:**
1. Use Glob to understand the project structure (top-level directories, package.json/Cargo.toml/go.mod/etc.)
2. Identify the tech stack from config files
3. Search for code related to the issue keywords using Grep
4. Read relevant files to understand existing patterns

**For Feature/Enhancement:**
- Search for existing related functionality
- Identify files that will need modification
- Check for existing patterns to follow
- Review related tests

**For Bug:**
- Locate the affected code paths
- Identify potential root cause locations
- Check for related error handling
- Find relevant test files

**For Task/Refactor:**
- Map the affected code surface area
- Identify dependencies on the code being changed
- Check for existing tests that verify current behavior

### 4. Clarification Questions

If analysis reveals ambiguities, ask the user BEFORE creating the plan.

**Ask about:**
- Undefined acceptance criteria
- Approach choices when multiple valid options exist
- Scope boundaries that seem unclear
- Missing technical constraints

**Do NOT ask about:**
- Things already clear in the issue
- Standard patterns visible in the codebase
- Obvious implementation details

### 5. Create Branch

Follow the naming convention:

```bash
git checkout -b issue/{NUMBER}-{short-kebab-case-description}
```

**Rules:**
- Always include issue number
- Use lowercase with hyphens
- Keep the description short but identifiable
- No special characters

**Examples:**
- Issue #42 "Dashboard: Add empty states" → `issue/42-dashboard-empty-states`
- Issue #15 "Fix timer drift on pause" → `issue/15-fix-timer-drift`

### 6. Generate Implementation Plan

Structure the plan based on what the project actually uses. Discover layers from the codebase rather than assuming them.

```markdown
## Implementation Plan for #{NUMBER}: {Title}

### Branch
`issue/{NUMBER}-{description}`

### Summary
[Brief description of what will be implemented and why]

### Files to Create/Modify
- `path/to/file1` — Description of changes
- `path/to/file2` — Description of changes

### Implementation Steps
1. [Concrete, ordered step]
2. [Next step]
3. [Continue...]

### Testing Strategy
- [What to test and how, based on project's test setup]
- [Edge cases from acceptance criteria]

### Documentation Updates
[If `docs-sync-required` label exists, list which docs need updating]

### Acceptance Criteria Verification
[Copy from issue with notes on how each will be verified]
```

### 7. Present Plan & Await Approval

Present the complete plan to the user. Ask:

> Ready to start implementation? I'll work through the plan step by step.

If the user wants changes, revise the plan. Do NOT begin implementation without explicit approval.

When approved, use EnterPlanMode to formally track the implementation.

# ui-critic

A Claude Code plugin that analyzes UI/web screenshots and provides structured, actionable design improvement suggestions grounded in established design principles.

## What It Does

Paste or point to a UI screenshot and get a detailed design critique covering visual hierarchy, typography, color harmony, spacing, component consistency, and responsiveness. Each suggestion includes concrete values (hex colors, pixel sizes, CSS properties) — not vague advice.

## Command

| Command | Description |
|---------|-------------|
| `/ui-critic:analyze` | Analyze a UI screenshot and provide design improvement suggestions |

### Usage

```bash
# With a pasted screenshot
/ui-critic:analyze this is a dashboard for a finance app

# With a file path
/ui-critic:analyze /path/to/screenshot.png

# Focus on specific areas
/ui-critic:analyze /path/to/screenshot.png focus on typography and color contrast

# Pasted screenshot with no extra context
/ui-critic:analyze
```

## Skill

The **design-principles** skill activates automatically when you discuss UI design review, visual hierarchy, typography, color contrast, spacing, or layout analysis in conversation.

## Design Dimensions Analyzed

1. **Hierarchy & Visual Weight** — focal points, competing elements, reading flow
2. **Typography** — font consistency, sizing, kerning, readability
3. **Color Harmony & Contrast** — palette cohesion, WCAG compliance, semantic usage
4. **Spacing & Layout Structure** — grid consistency, grouping, alignment
5. **Component Consistency** — buttons, cards, icons, state indicators
6. **Responsiveness Concerns** — fixed widths, touch targets, text overflow
7. **Additional Observations** — empty states, loading, accessibility

## Installation

### Local testing

```bash
claude --plugin-dir /path/to/ui-critic
```

### From marketplace

```bash
/plugin marketplace add marco-machado/code-plugins
/plugin install ui-critic@code-plugins
```

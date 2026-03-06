---
name: design-principles
description: |
  Use when reviewing, critiquing, or analyzing UI/web designs, screenshots, or interface mockups. Triggers on: design feedback, screenshot analysis, visual hierarchy review, color contrast checks, typography critique, spacing/alignment issues, component consistency, layout review, accessibility evaluation, responsive design concerns.
---

# UI Design Analysis Principles

Structured methodology for analyzing UI/web screenshots and providing actionable design improvements grounded in established design principles.

## Quick Reference

| Dimension | Key Checks |
|-----------|------------|
| **Hierarchy** | Focal point, competing elements, reading flow (F/Z-pattern) |
| **Typography** | Font consistency, type scale (3-5 sizes), line height, contrast |
| **Color** | Palette cohesion, WCAG contrast (4.5:1 / 3:1), semantic usage, color overload |
| **Spacing** | Grid system (4/8px), element grouping, alignment, density |
| **Components** | Button styles, card patterns, icon sizing, state indicators |
| **Responsiveness** | Fixed widths, touch targets (44x44px min), text overflow |
| **Additional** | Empty/loading/error states, accessibility, micro-interactions |

## Analysis Dimensions

Evaluate in order. Skip any dimension not applicable to the screenshot.

### 1. Hierarchy & Visual Weight

- **Primary focal point** — is the intended element drawing attention first?
- **Competing elements** — multiple bold colors, oversized controls, redundant CTAs fighting for attention
- **Active states** — visually distinct enough from inactive states?
- **Reading flow** — F-pattern (text-heavy) or Z-pattern (landing pages) supported by layout

### 2. Typography

- **Font consistency** — mixed typefaces within UI data components (branding fonts acceptable in logos/headers only)
- **Size scale** — coherent type scale, typically 3-5 sizes max
- **Line height** — body 1.4-1.6, headings 1.1-1.3
- **Kerning** — overly wide/tight letter-spacing, especially in numeric displays
- **Label sizing** — secondary labels minimum 11-12px
- **Tabular figures** — numeric data (tables, timers, prices) should use monospaced/tabular figures
- **Contrast** — text meets minimum contrast against background

### 3. Color Harmony & Contrast

- **Palette cohesion** — colors share consistent tone/saturation
- **WCAG contrast** — 4.5:1 for normal text, 3:1 for large text
- **Semantic usage** — green=success, red=error, orange/yellow=warning used consistently
- **Color overload** — recommend 1 primary + 1 accent max
- **Interactive indicators** — buttons/links distinguishable from decorative colored elements
- **Dark mode** — surface elevation via subtle brightness, not just borders

### 4. Spacing & Layout Structure

- **Spacing scale** — consistent base unit (4px or 8px grid)
- **Element grouping** — related items grouped tighter; distinct sections separated more
- **Alignment** — horizontal/vertical alignment across rows and columns
- **Content density** — appropriate for the interface's purpose
- **Floating elements** — data/labels disconnected from their context

### 5. Component Consistency

- **Button styles** — same action level uses same style across the interface
- **Card patterns** — similar data cards follow identical internal layouts
- **Icon sizing** — consistent within same context
- **Border usage** — consistent radius, weight, and color across similar components
- **State indicators** — active, hover, disabled, selected follow uniform visual language
- **Form elements** — inputs, dropdowns, toggles share consistent height, padding, styling

### 6. Responsiveness Concerns

- **Fixed widths** — absolute widths that break on smaller screens
- **Touch targets** — minimum 44x44px for mobile
- **Text overflow** — truncation or wrapping strategies for long strings
- **Layout breakpoints** — layouts needing restructuring at tablet/mobile
- **Scroll behavior** — horizontal scroll or content cutoff issues

### 7. Additional Observations

- **Empty states** — zero-data scenarios accounted for
- **Loading states** — skeleton screens or spinners needed
- **Error states** — visibility and helpfulness of error feedback
- **Micro-interactions** — opportunities for meaningful animation
- **Accessibility beyond color** — icon-only buttons without labels, focus indicators, screen reader considerations

## Output Format

### Opening Assessment

2-3 sentences: app type/purpose, qualitative assessment, what works well, key improvement themes. No numeric scores.

### Numbered Sections

For each applicable dimension:

**N. Dimension Name**

**Current State:** Describe what exists — reference actual UI elements visible in the screenshot.

**Suggestions:**
- **[Specific change]:** What to change and why. Include concrete values (hex colors, pixel sizes, CSS properties) when possible.

### Summary / Action Plan

End with prioritized list of the most impactful changes, ordered by visual impact, with specific values.

## Analysis Guidelines

- **Be specific** — reference actual elements ("the orange Active tab", "the timer digits 1:36:11"), not generics
- **Provide concrete values** — hex colors, pixel sizes, font weights, spacing values
- **Explain the why** — connect to design principles (visual hierarchy, Gestalt grouping, WCAG, Fitts's law)
- **Acknowledge strengths** — note what works before suggesting improvements
- **Prioritize impact** — lead each section with the most impactful suggestion
- **Consider context** — dashboard vs marketing page, mobile vs desktop
- **Respect design direction** — improve within existing design language, don't propose complete redesigns
- **Focus on requested areas** — when user specifies aspects to review, prioritize those dimensions

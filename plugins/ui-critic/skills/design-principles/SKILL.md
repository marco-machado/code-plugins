---
name: design-principles
description: |
  This skill should be used when the user asks to "review a UI design", "analyze a screenshot", "improve my interface", "critique this design", "check my layout", "review visual hierarchy", "analyze typography", "check color contrast", "review spacing and alignment", "what do you think of this design", or asks about UI/web design principles, visual hierarchy, color theory, typography best practices, layout patterns, or accessibility considerations.

  Triggers on topics like: "design feedback", "UI review", "screenshot analysis", "design improvements", "visual hierarchy", "color harmony", "typography issues", "spacing problems", "alignment issues", "component consistency", "responsive design concerns".
version: 0.1.0
---

# UI Design Analysis Principles

A structured methodology for analyzing UI/web screenshots and providing actionable design improvement suggestions grounded in established design principles.

## Analysis Framework

Every UI analysis follows a consistent structure. Examine the screenshot and evaluate each design dimension independently, then synthesize findings into a prioritized action plan.

### Analysis Dimensions

Evaluate the following dimensions in order. Skip any dimension that is not applicable to the given screenshot.

#### 1. Hierarchy & Visual Weight

Assess how effectively the design guides the user's eye through content.

- **Primary focal point**: Identify what draws attention first — is it the intended element?
- **Competing elements**: Flag elements that fight for attention (multiple bold colors, oversized controls, redundant CTAs)
- **Active states**: Check whether active/selected states are visually distinct enough from inactive states
- **Redundant controls**: Identify duplicate interactive elements that could be consolidated
- **Reading flow**: Verify the natural scan path (F-pattern for text-heavy, Z-pattern for landing pages) is supported by the layout

#### 2. Typography

Evaluate typeface usage, sizing, and readability.

- **Font consistency**: Flag mixed typeface usage within UI data components (branding fonts are acceptable in logos/headers only)
- **Size scale**: Check for a coherent type scale (typically 3-5 sizes maximum)
- **Line height**: Body text should have 1.4-1.6 line height; headings 1.1-1.3
- **Kerning and spacing**: Look for overly wide or tight letter-spacing, especially in numeric displays (timers, prices, dates)
- **Label sizing**: Secondary labels should be at minimum 11-12px for readability
- **Tabular figures**: Numeric data (tables, timers, prices) should use monospaced/tabular figures for alignment
- **Contrast**: Ensure text meets minimum contrast requirements against its background

#### 3. Color Harmony & Contrast

Evaluate the color palette for cohesion, accessibility, and intentional use.

- **Palette cohesion**: Check that colors share a consistent tone/saturation level
- **WCAG contrast**: Flag secondary/muted text that likely fails AA contrast (4.5:1 for normal text, 3:1 for large text) against its background
- **Semantic color usage**: Verify colors carry consistent meaning (green = success/active, red = error/destructive, orange/yellow = warning)
- **Color overload**: Flag when too many accent colors create visual noise — recommend reducing to 1 primary + 1 accent maximum
- **Interactive indicators**: Check that interactive elements (buttons, links, toggles) are distinguishable from decorative colored elements (dots, badges, tags)
- **Dark mode specifics**: In dark themes, check that surface elevation is communicated through subtle brightness differences, not just borders

#### 4. Spacing & Layout Structure

Evaluate whitespace usage, alignment, and structural clarity.

- **Consistent spacing scale**: Check for a base unit system (4px or 8px grid)
- **Element grouping**: Related items should be visually grouped with tighter spacing; distinct sections separated with more space
- **Alignment**: Verify horizontal and vertical alignment across rows and columns — flag uneven gaps between action items
- **Content density**: Check whether the layout feels cramped or too sparse for its purpose
- **Floating elements**: Flag data or labels that appear disconnected from their context (e.g., stats without values, labels far from their data points)
- **Header/navigation**: Assess navigation clustering — items should feel grouped, not scattered

#### 5. Component Consistency

Evaluate consistency in interactive and structural components.

- **Button styles**: Same action level should use same button style across the interface
- **Card patterns**: Cards with similar data should follow identical internal layouts
- **Icon sizing**: Icons within the same context should share a consistent size
- **Border usage**: Check for consistent border radius, weight, and color across similar components
- **State indicators**: Active, hover, disabled, and selected states should follow a uniform visual language
- **Form elements**: Inputs, dropdowns, and toggles should share consistent height, padding, and styling

#### 6. Responsiveness Concerns

Assess potential issues at different viewport sizes (when applicable).

- **Fixed widths**: Flag elements with absolute widths that will break on smaller screens
- **Touch targets**: Interactive elements should be minimum 44x44px for mobile
- **Text overflow**: Long strings (names, titles, URLs) should have truncation or wrapping strategies
- **Layout breakpoints**: Identify layouts that will need restructuring at tablet/mobile widths
- **Scroll behavior**: Flag potential issues with horizontal scroll or content being cut off

#### 7. Additional Observations

Catch design concerns that fall outside the core dimensions.

- **Empty states**: Check if the design accounts for zero-data scenarios
- **Loading states**: Consider whether skeleton screens or spinners are needed
- **Error states**: Assess visibility and helpfulness of error feedback
- **Micro-interactions**: Note opportunities for meaningful animation or transition
- **Accessibility beyond color**: Check for icon-only buttons without labels, focus indicators, screen reader considerations

## Output Template

Structure every analysis response following this format:

### Opening Assessment

Start with a brief overall assessment (2-3 sentences). Mention the app type/purpose if identifiable. Acknowledge what works well before diving into improvements. Do NOT provide a numeric score or rating.

Example:
> Based on the provided image, here is an analysis of your UI design for "[App Name]." Overall, it is a [qualitative assessment] interface. The [positive aspect] works well, but there are areas where [key improvement themes] can be refined.

### Numbered Sections

For each applicable dimension, use this structure:

```
### N. Dimension Name
**Current State:**
[Describe what exists — be specific about elements, colors, positions. Reference actual UI elements visible in the screenshot.]

**Suggestions:**
*   **[Specific change]:** [Detailed explanation of what to change and why. Include concrete values (hex colors, pixel sizes, specific CSS properties) when possible.]
*   **[Another change]:** [Explanation with specifics.]
```

### Summary / Action Plan

End with a prioritized list of the most impactful changes, ordered by visual impact:

```
### Summary of Refined Vision (Action Plan)

If implementing a refined version, here are the immediate changes ordered by impact:

1.  **[Most impactful change]** — [brief description with specific values]
2.  **[Next change]** — [brief description]
3.  ...
```

## Analysis Guidelines

- **Be specific, not vague**: Reference actual elements visible in the screenshot ("the orange Active tab", "the timer digits 1:36:11") rather than speaking generically
- **Provide concrete values**: Suggest specific hex colors, pixel sizes, font weights, spacing values — not just "make it bigger" or "lighten the color"
- **Explain the why**: Connect suggestions to design principles (visual hierarchy, Gestalt grouping, WCAG accessibility, Fitts's law)
- **Acknowledge strengths**: Note what the design does well before suggesting improvements
- **Prioritize impact**: Lead each section with the most impactful suggestions
- **Consider context**: A dashboard has different needs than a marketing page; a mobile app differs from a desktop tool
- **Respect the design direction**: Suggest improvements within the existing design language rather than proposing a complete redesign
- **Focus on requested areas**: When the user specifies particular aspects to review, prioritize those dimensions while briefly noting critical issues in other areas

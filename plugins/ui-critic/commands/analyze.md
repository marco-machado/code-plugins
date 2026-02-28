---
description: Analyze a UI/web screenshot and provide actionable design improvement suggestions
argument-hint: "[image path or paste screenshot] [optional: context or focus areas]"
allowed-tools: ["Read"]
---

# Analyze UI Design

Analyze the provided UI/web screenshot and deliver a structured design critique with actionable improvement suggestions.

## Input

```text
$ARGUMENTS
```

## Instructions

### 1. Obtain the Image

Determine how the image was provided:

- **Pasted image**: The screenshot is already visible in the conversation — proceed directly to analysis.
- **File path provided**: Use the Read tool to view the image file at the specified path. If the file does not exist or is not a valid image, inform the user and ask for a valid path or pasted screenshot.
- **No image provided**: Ask the user to either paste a screenshot or provide a file path.

### 2. Parse Optional Context

Extract any additional context from the arguments:

- **App context**: Description of what the app/page is (e.g., "finance dashboard", "e-commerce checkout")
- **Focus areas**: Specific design dimensions to prioritize (e.g., "focus on typography and color", "just check spacing")

If focus areas are specified, prioritize those dimensions in the analysis while still briefly noting any critical issues found in other areas.

### 3. Analyze the Design

Apply the design-principles skill to evaluate the screenshot. Examine each applicable dimension:

1. **Hierarchy & Visual Weight** — focal points, competing elements, reading flow
2. **Typography** — font consistency, sizing, kerning, readability
3. **Color Harmony & Contrast** — palette cohesion, WCAG compliance, semantic usage
4. **Spacing & Layout Structure** — grid consistency, grouping, alignment
5. **Component Consistency** — buttons, cards, icons, states
6. **Responsiveness Concerns** — fixed widths, touch targets, overflow
7. **Additional Observations** — empty states, loading, accessibility

Skip dimensions that are not applicable to the screenshot. When the user requested specific focus areas, cover those in full depth and only briefly mention critical issues in other dimensions.

### 4. Structure the Response

Follow the output template from the design-principles skill:

1. **Opening assessment**: Brief qualitative overview (2-3 sentences). Acknowledge what works well. Do NOT include a numeric score or rating.
2. **Numbered sections**: One per applicable dimension, each with "Current State" and "Suggestions" subsections. Be specific — reference actual UI elements, suggest concrete values (hex colors, pixel sizes, CSS properties).
3. **Summary / Action Plan**: Prioritized list of the most impactful changes, ordered by visual impact, with specific values.

### 5. Deliver the Analysis

Present the complete analysis in a single, well-formatted response. Use markdown headers, bold text, and bullet points for readability.

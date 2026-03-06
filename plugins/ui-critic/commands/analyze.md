---
description: Analyze a UI/web screenshot and provide actionable design improvement suggestions
argument-hint: "[image path or paste screenshot] [optional: focus areas]"
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

- **Pasted image**: Proceed directly to analysis.
- **File path provided**: Use the Read tool to view the image file. If invalid, ask for a valid path or pasted screenshot.
- **No image provided**: Ask the user to paste a screenshot or provide a file path.

### 2. Parse Optional Context

Extract from arguments:
- **App context**: What the app/page is (e.g., "finance dashboard", "e-commerce checkout")
- **Focus areas**: Specific dimensions to prioritize (e.g., "typography and color", "just spacing")

### 3. Analyze and Respond

Apply the design-principles skill to evaluate the screenshot across all applicable dimensions. When focus areas are specified, cover those in full depth and only briefly mention critical issues in other dimensions.

Follow the output format defined in the design-principles skill: opening assessment, numbered sections with current state and suggestions, and a prioritized action plan.

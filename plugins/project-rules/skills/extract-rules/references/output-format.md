# Rule File Output Format

## File template

Each category writes one file at `.claude/rules/<category>.md`:

```markdown
# <Category title> rules

<!-- Maintained by /project-rules:extract-rules. Manual edits are preserved on re-runs
     unless the codebase contradicts them. Last analyzed: <YYYY-MM-DD> -->

- <Rule statement.> <Optional brief example or scope note.>
- <Rule statement.>

## Tool-enforced

- <Dimension> is enforced by <tool>; see <config file>.
```

Omit the "Tool-enforced" section when nothing applies. Update the "Last analyzed" date on every run.

## Writing rules

These files load into every future session's context, so optimize for density:

- One bullet per rule, one sentence per bullet where possible. Imperative form: "Use absolute imports from `src/`", not "Absolute imports are used".
- Be specific enough to act on. "Name test files `<module>.test.ts` next to the module" beats "follow test naming conventions".
- Include a terse example only when the rule is ambiguous without one: `Wrap errors when crossing module boundaries: fmt.Errorf("loading config: %w", err)`.
- Scope exceptions inline: "Use the shared `logger`; plain `console.log` is acceptable in `scripts/`."
- In monorepos or multi-language projects, group rules under a subheading per area when they differ.
- Target 5 to 15 rules per file. Past that, merge related rules or cut the weakest; a bloated rule file gets ignored.
- Never include generic best practices the codebase does not demonstrably follow, rules about things a formatter already fixes, or hedged language ("consider", "prefer when possible") — a rule either holds or it is not a rule.

## Update semantics

When the file already exists:

- Match existing bullets to new findings by meaning, not exact text.
- Confirmed rules: keep the existing wording unless the new finding is materially more precise.
- Unconfirmed but uncontradicted rules: keep verbatim — they are likely deliberate manual edits.
- Contradicted rules: replace, and list the before/after in the chat report.
- Preserve any sections or prose the user added beyond the bullet list.

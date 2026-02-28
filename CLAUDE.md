# CLAUDE.md

## Repository Structure

Multi-plugin marketplace repo. Each plugin lives under `plugins/{name}/` with its own `.claude-plugin/plugin.json`. The root `.claude-plugin/marketplace.json` indexes all plugins with matching versions.

```
code-plugins/
├── .claude-plugin/marketplace.json   # Marketplace manifest (versions must match plugin.json)
├── .github/workflows/
│   └── version-bump.yml              # Auto version bump on merge to main
├── plugins/
│   └── {name}/
│       ├── .claude-plugin/plugin.json
│       ├── commands/
│       ├── agents/
│       ├── skills/
│       └── ...
└── CLAUDE.md
```

## Version Bumping

Versions are automatically bumped by GitHub Actions on every merge to `main` that touches files under `plugins/`.

**Bump rules:**
- `feat:` or `feat(scope):` commit → **minor** bump (0.1.0 → 0.2.0)
- Everything else (`fix:`, `chore:`, `docs:`, etc.) → **patch** bump (0.1.0 → 0.1.1)

**Skip conditions (no bump occurs when):**
- Commit message starts with `chore: bump ` (loop prevention)
- Commit message contains `[skip-version]`
- No files changed under `plugins/`
- Only `plugin.json` itself changed in a plugin dir

**Both files must always have matching versions:**
- `plugins/{name}/.claude-plugin/plugin.json` → `"version"` field
- `.claude-plugin/marketplace.json` → `plugins[].version` for matching `name`

When manually editing versions, update both files. The CI handles this automatically for normal merges.

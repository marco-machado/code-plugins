# Extracting Platform Compatibility Conventions

## What to examine

This category is about the environments the code must run on and the habits that keep it portable:

- **Runtime version constraints**: `engines` in package.json, `.nvmrc`/`.node-version`, `requires-python`, `go.mod` go directive, `rust-version`, `.tool-versions`, Dockerfile base images. The declared floor is the rule; code using newer features than the floor allows is a finding.
- **Target platforms**: CI matrix in workflow files (which OSes and versions are actually tested), `browserslist`, deployment targets in Dockerfiles or infra configs, mobile OS minimums.
- **Path handling**: `path.join`/`pathlib`/`filepath.Join` vs hand-concatenated separators; hardcoded `/` or `\\`; assumptions about case-sensitive filesystems; use of `os.homedir()`/`tempfile` vs hardcoded locations.
- **Shell and scripts**: shebangs (`#!/bin/sh` vs `#!/usr/bin/env bash`); bashisms in sh scripts; GNU-only flags (`sed -i`, `grep -P`, `date -d`) that break on macOS/BSD; whether npm scripts and Makefiles avoid shell-specific syntax; any use of cross-platform helpers (`cross-env`, `rimraf`, `shx`).
- **Line endings and encoding**: `.gitattributes` rules, `.editorconfig` `end_of_line`, explicit encodings in file reads.
- **Environment access**: how env vars are read and validated; `.env` file conventions; defaults for missing vars; platform detection code (`process.platform`, `runtime.GOOS`, `sys.platform`) and what it guards.
- **Feature gating**: polyfills, transpilation targets (tsconfig `target`, Babel presets), conditional compilation, build tags.

## Convention vs accident

- If CI only tests Linux but developers use macOS, the de facto rule is "works on both"; check scripts for portability accordingly.
- One hardcoded `/tmp` in a codebase of `tempfile` usage is an accident; flag it.
- Version floors that contradict each other (engines says 18, CI tests 20 only) are findings to report, not rules to copy.

## What to report

Rules such as: minimum runtime versions and where they are declared, the OSes code and scripts must support, required path-handling APIs, shell dialect for scripts and flags to avoid, env var access pattern, and browser or deployment targets. State each rule with its source of truth (the file that declares it) so future changes update both.

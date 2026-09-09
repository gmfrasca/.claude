---
name: lint
description: >-
  Run linting checks on the current project. Discovers project-specific lint
  commands from contributing guides, Makefiles, or CI configs, then falls back
  to language-specific defaults. Use when the user says "lint", asks to lint,
  or wants to check code style.
---

# Lint

## Workflow

### 1. Discover project lint rules

Check for contributing/lint guidance (stop at first match):

1. `CONTRIBUTING.md`, `CONTRIBUTING`, `.github/CONTRIBUTING.md`
2. `Makefile` — look for targets: `lint`, `check`, `verify`, `fmt`
3. `package.json` — look for `scripts.lint`, `scripts.check`
4. `.golangci.yml` / `.golangci.yaml` — confirms golangci-lint usage
5. CI configs (`.github/workflows/*.yml`, `Makefile`) for lint invocations

Record the discovered lint command(s) for step 2.

### 2. Run linting

**If a project lint command was found**, run it. Common examples:

- `make lint`
- `npm run lint`
- `golangci-lint run ./...`

**If no project command was found**, run language-appropriate defaults:

| Language | Command |
|----------|---------|
| Go | `go vet ./...` |
| Python | `ruff check .` or `flake8 .` |
| TypeScript/JavaScript | `npx eslint .` |
| Rust | `cargo clippy` |
| Shell | `shellcheck *.sh` |

Scope to changed files when possible (use `git diff --name-only` to identify them).

### 3. Report results

- If clean: confirm no issues found.
- If issues found: list them grouped by file, with the severity and line number. Offer to fix auto-fixable issues.

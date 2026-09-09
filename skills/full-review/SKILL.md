---
name: full-review
description: >-
  Perform a code review with lint pre-flight and sensitive data scan on
  current local changes. Use when the user asks for a code review, says
  "review my changes", "full review", or "code review".
---

# Full Code Review

Perform a code review including pre-flight checks on the current local changes.

## Steps

### 1. Lint

- Discover project lint rules by scanning:
  - `Makefile` targets (`lint`, `verify`, `check`, `fmt`)
  - CI configs (`.github/workflows/`, `.golangci.yml`, etc.)
  - `package.json` scripts (`lint`, `check`)
- Run the project's lint command scoped to changed files.
- If lint issues are found, report them as a finding.

### 2. Sensitive Data Scan

Scan every added or modified line for:

| Pattern | Example |
|---------|---------|
| API keys / tokens | `AKIA...`, `ghp_...`, `Bearer eyJ...` |
| Passwords | `password=`, `secret:` in non-test code |
| Connection strings | `postgres://user:pass@`, `mongodb+srv://` |
| Private keys | `-----BEGIN RSA PRIVATE KEY-----` |
| Cloud credentials | Azure client secrets, AWS secret keys, GCP SA JSON |

Exceptions (do not flag):
- References to environment variables (`$SECRET`, `${PASSWORD}`)
- Placeholder values (`changeme`, `REPLACE_ME`, `<redacted>`)
- Test fixtures in `*_test.go`, `testdata/`, or `fixtures/`

### 3. Code Review

- Review the relevant code with a code review mindset.
- Run the full review even if no changes were made since the last review.
- Prioritize bugs, behavioral regressions, security issues, and missing tests.
- Findings must be the primary focus, ordered by severity.
- Do not make code changes unless the user explicitly asks for them.

## Output Format

Present findings in a numbered table:

```
| # | Severity | File:Line | Title | Details |
|---|----------|-----------|-------|---------|
| 1 | High     | foo.go:42 | ...   | ...     |
```

### Severity Definitions

| Severity | Criteria |
|----------|----------|
| Critical | Data loss, security vulnerability, credential leak, crash in production path |
| High | Bug under normal usage, missing error handling on critical path |
| Medium | Edge-case bug, suboptimal design, missing test coverage |
| Low | Style inconsistency, minor code smell, optional improvement |
| Informational | Observation, positive feedback, suggestion for future work |

# Review Standards

Detailed criteria for each review check. Referenced by [SKILL.md](SKILL.md).

## Lint Preflight

1. Discover the project's lint tooling by scanning for:
   - `Makefile` targets (`lint`, `verify`, `check`)
   - CI config (`.github/workflows/`, `ci-operator/`, `.golangci.yml`, etc.)
   - `package.json` scripts (`lint`, `check`)
2. Run the linter scoped to changed packages/files only.
   - For Go: prefer `go vet ./changed/pkg/...`; use `golangci-lint run` if
     available and the Go version is compatible.
   - For other languages: use the project's configured linter.
3. Report each lint issue as a finding (severity: Low unless it indicates a bug).

**Important**: Run lint commands in read-only fashion. Never run auto-fix
(`--fix`, `fmt -w`, etc.).

## Sensitive Data Scan

Scan every added or modified line in the diff for:

| Pattern | Example |
|---------|---------|
| API keys / tokens | `AKIA...`, `ghp_...`, `Bearer eyJ...` |
| Passwords | `password=`, `passwd:`, `secret:` in non-test code |
| Connection strings | `postgres://user:pass@`, `mongodb+srv://` |
| Private keys | `-----BEGIN RSA PRIVATE KEY-----` |
| Cloud credentials | Azure client secrets, AWS secret access keys, GCP service account JSON |
| PII | Email addresses, IP addresses in non-test hardcoded strings |

Exceptions (not flagged):
- References to environment variables (`$SECRET`, `${PASSWORD}`)
- Placeholder values (`changeme`, `REPLACE_ME`, `<redacted>`)
- Test fixtures clearly in `*_test.go`, `testdata/`, or `fixtures/`

Report each match as a finding (severity: Critical for real credentials,
High for likely credentials, Medium for ambiguous patterns).

## Code Review Criteria

### Correctness
- Logic errors, off-by-one, nil/null dereference, race conditions
- Incorrect error handling (swallowed errors, wrong error types)
- Missing input validation or boundary checks
- Broken contracts (changed interfaces without updating callers)

### Security
- Injection vulnerabilities (SQL, command, template)
- Improper authentication or authorization checks
- Hardcoded secrets (overlaps with sensitive data scan)
- Unsafe deserialization or file path handling

### Reliability
- Resource leaks (unclosed connections, file handles, goroutine leaks)
- Missing context propagation or cancellation
- Panics in library code (should return errors instead)
- Missing or incorrect retry/backoff logic

### Testing
- Are new code paths covered by tests?
- Do tests validate error/edge cases, not just the happy path?
- Are test assertions specific (not just "no error")?
- Time-sensitive tests (hardcoded dates, `time.Now()` dependencies)

### Maintainability
- Unnecessary complexity or premature abstraction
- Dead code, unused imports, redundant comments
- Inconsistent naming or style within the PR
- Missing or misleading documentation for public APIs

## Architecture Review Criteria

### Design
- Does the change fit the existing architecture or introduce a new pattern?
- Is the scope appropriate, or does it mix unrelated concerns?
- Are responsibilities cleanly separated (single responsibility)?
- Could the change be decomposed into smaller, independently reviewable units?

### Dependencies
- New dependencies: are they justified, maintained, and license-compatible?
- Are dependency boundaries respected (no circular imports, no reaching into
  internal packages)?
- Is the coupling between components appropriate?

### API Surface
- Are public APIs minimal and consistent with existing conventions?
- Are breaking changes flagged and justified?
- Is backward compatibility maintained where expected?

### Performance & Scalability
- Obvious O(n^2) or worse algorithms on unbounded input
- Missing pagination, unbounded queries, or full-table scans
- Memory allocation patterns that could cause pressure under load
- Missing caching where repeated expensive operations occur

### Observability
- Are errors logged with sufficient context for debugging?
- Are new operations instrumented (metrics, traces, structured logs)?
- Are failure modes observable (health checks, alerting)?

## Severity Definitions

| Severity | Criteria |
|----------|----------|
| **Critical** | Data loss, security vulnerability, credential leak, or crash in production path |
| **High** | Bug that will manifest under normal usage, missing error handling on critical path, architectural anti-pattern that will be costly to fix later |
| **Medium** | Edge-case bug, suboptimal design that may cause issues at scale, missing test coverage for important paths |
| **Low** | Style inconsistency, minor code smell, optional improvement |
| **Informational** | Observation, positive feedback, or suggestion for future work |

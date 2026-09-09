---
description: "Code review with lint check"
---
## Task
Perform a code review including pre-flight checks like lint on the current changes.

$ARGUMENTS

## Steps

### 1. Lint
- Discover project lint rules (Makefile, contributing docs, CI configs)
- Run the project's lint command scoped to changed files
- If lint issues are found, report them as a finding

### 2. Sensitive Data Scan
- Use the skill "sensitive-scan.md" to detect any sensitive data that could potentially be leaked

### 3. Code Review
- Review the relevant code with a code review mindset.
- Run full review even if no changes were made since last review
- Prioritize bugs, behavioral regressions, security issues, and missing tests. 
- Findings must be the primary focus, ordered by severity. 
- Do not make code changes unless the user explicitly asks for them.

---
name: review-pr
description: >-
  Perform an integrated code and architecture review of a GitHub pull request.
  Fetches the PR diff remotely via gh CLI, runs lint and sensitive-data checks,
  and produces a structured findings report — all without modifying local files.
  Use when the user asks to review a PR, passes a PR URL, or says "review PR".
disable-model-invocation: true
---

# PR Review Skill

Review a GitHub pull request for code quality, architectural soundness, and
security — **without touching the local working tree**.

## Inputs

The user provides one of:
- A PR URL (`https://github.com/org/repo/pull/123`)
- A PR number (`#123` or `123`) — `org/repo` is inferred from the current git remote

## Hard Constraints

- **Read-only**: NEVER check out, merge, cherry-pick, or apply the PR diff
  locally. All analysis is performed on the diff fetched from GitHub.
- **No local modifications**: Do not run `git checkout`, `git stash`,
  `git apply`, or any command that alters the working tree, index, or HEAD.
- You MAY read local files that already exist (to understand surrounding
  context) using the Read tool.

## Workflow

### 1. Fetch PR metadata and diff

```bash
gh pr view <PR> --repo <org/repo> --json title,body,baseRefName,headRefName,files,additions,deletions,commits
gh pr diff <PR> --repo <org/repo>
```

Parse the output to build a mental model of:
- Total size (files changed, lines added/deleted)
- Which packages/directories are touched
- The stated intent from the PR title and body

### 2. Understand changed files

For each substantially changed file in the diff:
1. Read the **full current version** of the file from the local tree (if it
   exists) for surrounding context.
2. Analyze the hunks from the diff to understand what changed.

Group files by concern (e.g., production code, tests, config, infra).

### 3. Run checks

Run the checks described in [review-standards.md](review-standards.md):

- **Lint preflight** — discover and run the project's lint tooling scoped to
  changed files/packages. Report any lint issues as findings.
- **Sensitive data scan** — inspect the diff for secrets, credentials, tokens,
  connection strings, private keys, or PII.
- **Code & architecture review** — the main review pass (see below).

### 4. Integrated code & architecture review

Analyze the diff holistically, covering both code-level and architecture-level
concerns in a single pass. For detailed review criteria, see
[review-standards.md](review-standards.md).

Produce findings ordered by severity. Each finding must include:

| Field | Description |
|-------|-------------|
| **#** | Sequential number |
| **Severity** | Critical, High, Medium, Low, or Informational |
| **Title** | One-line summary |
| **Location** | File and line range (from the diff) |
| **Details** | What's wrong, why it matters, and a suggested fix |

### 5. Report

Output a structured review with these sections:

```
# PR Review: <title> (#<number>)

## Overview
- **Base**: <base branch> ← **Head**: <head branch>
- **Size**: <N files>, +<additions> / -<deletions>
- **Scope**: <1-sentence summary of what the PR does>

## Checks
| Check | Result |
|-------|--------|
| Lint  | Pass / <N issues> |
| Sensitive data | Clean / <N issues> |

## Findings

| # | Severity | Title |
|---|----------|-------|
| 1 | High     | ... |
| 2 | Medium   | ... |

### Finding 1 — <title>
**Severity**: High
**Location**: `path/to/file.go:42-58`
**Details**: ...

(repeat for each finding)

## Summary
<1-2 sentence overall assessment: ready to merge, needs minor fixes, or
needs significant rework>
```

If there are no findings, say so explicitly and confirm the PR is clean.

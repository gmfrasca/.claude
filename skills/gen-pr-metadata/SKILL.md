---
name: gen-pr-metadata
description: >-
  Generate a PR title and description from the current branch diff.
  Use when the user asks to generate PR metadata, write a PR description,
  prepare a pull request, or says "PR title" or "PR description".
---

# Generate PR Metadata

## Task

Review the git diff against the base branch and generate a PR title and description summarizing the changes.

## Workflow

1. Determine the base branch. Unless the user specifies otherwise, diff against the default branch:
   - `main`, `master`, or the tracked upstream remote equivalent.
2. Run `git diff <base>..HEAD` to inspect all changes on the current branch.
3. Analyze the diff and generate a PR title and description.

## Rules

- Always follow [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) formatting for the PR title.
  - Include a scope keyword when possible (e.g. `feat(scope): ...`).
- If the diff against the base branch is empty, notify the user and ask for clarity.
- If the repo defines a PR template (typically `PULL_REQUEST_TEMPLATE.md` or `.github/PULL_REQUEST_TEMPLATE.md`), follow and pre-populate it with relevant metadata.
- The description should explain the **why**, not just the **what**.

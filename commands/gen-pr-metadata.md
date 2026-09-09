---
description: "Generate PR Metadata"
allowed-tools:
  - Read
  - Bash(git diff)
---
## Task
Review the git diff and generate a PR title and description to summarize the changes.

## Context
Current change:
!`git diff`

## Rules
- Unless otherwise specified, assume the diff is against the default branch, ie:
    - main
    - master
    - <upstream_remote>/main
    - <upstream_remote>/master
  where <upstream_remote> is the main upstream remote on github the code typically targets


- Always follow [conventional commits](https://www.conventionalcommits.org/en/v1.0.0/) formatting for  PR title
    - Include scope keyword (ie `feat(scope): ...`) whenever possible

- If `git diff` vs the target base branch is empty, notify the requestor and ask for clarity

- If the repo defines a PR template (typically found in PULL_REQUEST_TEMPLATE.md), always follow and   pre-populate it with relevent metadata

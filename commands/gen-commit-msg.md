---
description: "Generate commit message"
allowed-tools:
  - Read
  - Bash(git diff)
---
## Task
Review the current git diff and generate a commit message summarizing the content

## Context
Current change:
!`git diff`

## Rules
- Always follow [conventional commits](https://www.conventionalcommits.org/en/v1.0.0/) formatting
  - Include scope keyword (ie `feat(scope): ...`) whenever possible

- If `git diff` is empty, notify the requestor, then generate a commit message for the changes in the previous commit

- Try to keep the message as concise as possible - only a few lines at most

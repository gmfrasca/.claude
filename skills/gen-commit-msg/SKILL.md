---
name: gen-commit-msg
description: >-
  Generate a conventional commit message from the current git diff.
  Use when the user asks to generate a commit message, write a commit message,
  or says "commit message".
---

# Generate Commit Message

## Task

Review the current git diff and generate a commit message summarizing the content.

## Workflow

1. Run `git diff` to inspect the current staged and unstaged changes.
2. If the diff is empty, check `git diff HEAD~1` for the most recent commit instead and notify the user.
3. Analyze the changes and write a commit message.

## Rules

- Always follow [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) formatting.
  - Include a scope keyword when possible (e.g. `feat(scope): ...`).
- Keep the message concise: a subject line plus at most a few body lines.
- If the diff is empty and there is no recent commit, notify the user and ask for clarity.

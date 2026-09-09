---
name: implement-finding
description: >-
  Implement code review finding(s) by number from a prior review in the
  current conversation. Use when the user says "implement finding 1",
  "fix finding 2", "address findings 1-3", or references numbered findings
  from an earlier code review.
---

# Implement Code Review Finding

## Task

Implement the code review finding(s) specified by the user from a prior review in this conversation.

## Context

The user provides finding number(s) from a previous code review (e.g. "1", "1-2", "1,3", "1 and 3").

## Workflow

1. Look back in the conversation for the most recent code review output.
2. Identify the finding(s) matching the requested number(s).
3. Read the referenced file and surrounding context.
4. Implement the fix described in each finding.
5. After all changes, run the project's build and test commands to verify nothing is broken.

## Rules

- Do NOT touch code unrelated to the specified finding(s).
- If a finding is purely informational with no clear fix (e.g. "consider doing X"), ask the user for confirmation before proceeding.
- If the finding is ambiguous, present options to the user before changing code.
- Summarize what was changed after completing all fixes.

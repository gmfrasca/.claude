---
description: "Implement code review finding(s) by number"
---
## Task
Implement the code review finding(s) specified by the user.

## Context
The user will provide finding number(s) from a previous `/code-review` in this conversation (e.g. "1", "1-2", "1,3", "1 and 3").

$ARGUMENTS

## Rules
- Look back in the conversation for the most recent `/code-review` output
- Identify the finding(s) matching the requested number(s)
- Implement the fix described in each finding
- After all changes, run the build and tests to verify nothing is broken
- Do NOT touch code unrelated to the specified finding(s)
- If a finding is purely informational with no clear fix (e.g. "consider doing X"), ask the user for confirmation before proceeding

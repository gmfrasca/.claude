---
name: address-pr-comments
description: Fetch PR review comments from GitHub using gh CLI, categorize them by type and severity, and recommend or implement fixes. Use when the user asks to address PR comments, review PR feedback, fix PR comments, or respond to reviewers.
---

# Address PR Comments

## Workflow

### 1. Identify the PR

Determine the PR to review:

- If a GitHub PR URL is provided, extract `owner/repo` and PR number from it
- If a PR number is provided, use the current repo context
- If neither, detect from the current branch:
  ```bash
  gh pr view --json number,url,headRefName 2>&1
  ```

### 2. Fetch review comments

```bash
# Get PR review comments (inline code comments)
gh api repos/{owner}/{repo}/pulls/{number}/reviews
gh api repos/{owner}/{repo}/pulls/{number}/comments

# Get general PR comments (conversation)
gh api repos/{owner}/{repo}/issues/{number}/comments
```

### 3. Categorize comments

Group each comment into one of these categories:

| Category | Icon | Action |
|----------|------|--------|
| **Change requested** | `CR` | Code change required — implement the fix |
| **Question** | `Q` | Reviewer asked a question — draft a reply |
| **Nit/style** | `N` | Minor style issue — implement if trivial |
| **Approval/praise** | `OK` | No action needed |
| **Stale/resolved** | `--` | Already addressed or outdated — skip |

Filter out bot comments and already-resolved threads.

### 4. Present findings

Summarize in a table:

```
| # | Category | File:Line | Reviewer | Summary | Action |
|---|----------|-----------|----------|---------|--------|
| 1 | CR       | foo.go:42 | @alice   | nil check missing | Implement fix |
| 2 | Q        | bar.go:10 | @bob     | Why not use X? | Draft reply |
| 3 | N        | baz.go:5  | @alice   | Rename var | Implement fix |
```

### 5. Implement fixes

For each actionable comment:
1. Read the referenced file and surrounding context
2. Implement the fix described by the reviewer
3. If the comment is ambiguous, present options to the user before changing code

For questions: draft a suggested reply but do not post it — present it to the user for approval.

### 6. Verify

After all fixes:
- Run the project's build/test commands to verify nothing is broken
- Summarize what was changed and what still needs the user's input (e.g. replies to post)

## Notes

- Never post replies or dismiss reviews automatically — only draft them
- If a comment references code that has since changed, flag it as potentially stale
- Respect `.gitignore` and don't modify generated files unless the comment specifically targets them


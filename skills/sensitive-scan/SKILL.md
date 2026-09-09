---
name: sensitive-scan
description: >-
  Scan the working tree and branch history for sensitive data such as secrets,
  credentials, tokens, keys, passwords, connection strings, and subscription IDs.
  Use when the user says /sensitive-scan, asks to check for secrets, or before
  pushing a branch.
disable-model-invocation: true
---
# Sensitive Data Scanner
## Workflow
### 1. Scan uncommitted changes
Check both staged and unstaged content:
```bash
git diff HEAD
git diff --cached
git ls-files --others --exclude-standard
For each changed or untracked file, scan the content for sensitive patterns (see patterns below).

2. Scan branch history
Compare all commits on the current branch against the base:

git log --oneline main..HEAD
git diff main..HEAD
Review the full diff for sensitive patterns. Also check commit messages.

3. Pattern matching
Scan for these categories. Use grep/ripgrep where possible, but also read and inspect files that could contain embedded secrets.

High confidence (always flag):

Private keys: -----BEGIN (RSA |EC |OPENSSH )?PRIVATE KEY-----
Azure client secrets: [a-zA-Z0-9~_.-]{34,} near clientSecret, client_secret, or AZURE_CLIENT_SECRET
Connection strings: AccountKey=, SharedAccessKey=, Password= in connection string format
Bearer tokens: Bearer [A-Za-z0-9\-._~+/]+=* (long strings)
JWT tokens: eyJ[A-Za-z0-9_-]{10,}\.eyJ[A-Za-z0-9_-]{10,}
AWS keys: AKIA[0-9A-Z]{16}
Generic API keys: ['\"]([A-Za-z0-9]{32,})['\"] near api_key, apikey, secret, token
Medium confidence (flag with context):

Azure subscription IDs: UUID patterns near subscriptionId, subscription_id, or in ARM resource paths
Tenant IDs: UUID patterns near tenantId, tenant_id
Hardcoded passwords: password\s*[:=]\s*['"][^'"]+['"] (not in test files with obviously fake values)
.env files or files named secrets, credentials, kubeconfig
Base64-encoded blobs > 40 chars that could be encoded secrets
Low confidence (mention only if suspicious):

IP addresses and internal hostnames
Email addresses in non-standard locations
4. Report results
Group findings by severity:

COMMITTED (in branch history) — highest priority, requires history rewrite to fix:

List each finding with commit hash, file, line, and what was found
Warn that git push will publish this
STAGED/UNSTAGED (not yet committed) — easy to fix:

List each finding with file, line, and what was found
Offer to help remove or gitignore
FALSE POSITIVES — note anything that looks sensitive but is likely safe:

Test fixtures with fake values
Documentation examples
Already-public values (e.g. well-known client IDs)
If nothing found, confirm the scan was clean.

5. Exclusions
Skip scanning:

go.sum, go.mod (dependency hashes, not secrets)
package-lock.json, yarn.lock
Binary files
Vendored dependencies
Files matching .gitignore patterns


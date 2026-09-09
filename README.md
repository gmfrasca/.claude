# AI Skills, Commands and Settings for General Development

A portable set of agent skills for general development and debugging.
Works across Cursor, VS Code + GitHub Copilot, Claude Code, and any
agent that supports the [agentskills.io](https://agentskills.io/specification)
open specification.

## Skills

All reusable workflows live in `skills/` as agentskills.io-compatible
`SKILL.md` directories. Both Cursor and VS Code + Copilot discover
`~/.claude/skills/` by default, so no extra configuration is needed.

| Skill | Description |
|-------|-------------|
| `gen-commit-msg` | Generate a conventional commit message from git diff |
| `gen-pr-metadata` | Generate PR title and description from branch diff |
| `lint` | Discover and run project-specific linting |
| `full-review` | Code review with lint preflight and sensitive data scan |
| `implement-finding` | Implement numbered findings from a prior review |
| `review-pr` | Review a GitHub PR remotely (read-only, via `gh` CLI) |
| `sensitive-scan` | Scan working tree and branch history for leaked secrets |
| `address-pr-comments` | Fetch and categorize PR review comments, implement fixes |
| `analyze-review` | Analyze findings from a separate reviewer agent |
| `coding-standards` | ARO-HCP coding conventions and style enforcement |
| `humanizer` | Remove signs of AI-generated writing from text |

## Installation

Clone this repo into `~/.claude`:

```bash
git clone git@github.com:gmfrasca/.claude.git ~/.claude
```

### Platform discovery paths

| Platform | Skills auto-discovered from |
|----------|---------------------------|
| Cursor | `~/.claude/skills/`, `~/.cursor/skills/` |
| VS Code + Copilot | `~/.claude/skills/`, `~/.copilot/skills/`, `.github/skills/` |
| Claude Code | `~/.claude/skills/` |
| Copilot CLI | `~/.copilot/skills/`, `~/.agents/skills/` |

Since `~/.claude/skills/` is read by all major platforms, no symlinks or
extra config are needed for skill discovery.

## Legacy commands

The `commands/` directory contains Cursor/Claude Code specific slash
commands (`.md` files). These have been superseded by the equivalent
skills above, which work across all platforms. The commands are kept
for backward compatibility.

## Frontmatter compatibility notes

Some skills use platform-specific frontmatter extensions:

- `disable-model-invocation`: Cursor-specific; silently ignored by Copilot
- `args`: Cursor-specific argument hint; silently ignored by Copilot
- `allowed-tools`: Part of the agentskills.io spec (experimental);
  tool names are platform-specific and unrecognized names are ignored

These do not cause errors on any platform.

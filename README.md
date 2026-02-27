# Bugfix Pipeline Plugin

A Claude Code plugin that automates the complete bug fix lifecycle:

**Sentry Issue** → **Root Cause Analysis** → **Dev Confirmation** → **ClickUp Task** → **Branch** → **Code Fix** → **Commit** → **Dev Review** → **PR** → **Status Update**

## What it does

When you encounter a Sentry error, this plugin guides Claude through:

1. **Investigating** the Sentry issue (error details, stacktrace, impact)
2. **Finding the root cause** in the codebase, then **pausing for developer confirmation**
3. **Creating a ClickUp task** in the developer-chosen sprint/folder
4. **Creating a branch** from master
5. **Fixing** the bug using TDD (test-driven development)
6. **Committing** with standardized message format
7. **Presenting the fix** to the developer for manual review, then **creating a PR** if approved
8. **Updating ClickUp task status** to PR - IN REVIEW

## Prerequisites

### MCP Servers

Configure these MCP servers in `~/.claude/settings.json`:

```json
{
  "mcpServers": {
    "sentry": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@sentry/mcp-server"],
      "env": {
        "SENTRY_AUTH_TOKEN": "<your-sentry-token>"
      }
    },
    "clickup": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "clickup-mcp-server"],
      "env": {
        "CLICKUP_API_TOKEN": "<your-clickup-token>"
      }
    }
  }
}
```

### CLI Tools

- **GitHub CLI** (`gh`): `brew install gh && gh auth login`
- **Git**: configured with remotes for your repository

## Installation

### Option 1: Local install (for testing)

```bash
# Clone the plugin
git clone <repo-url> ~/Sources/sbox-bugfix-pipeline

# Register as a local marketplace in Claude Code settings
# Add to ~/.claude/settings.json:
{
  "pluginMarketplaces": [
    "file:///Users/<you>/Sources/sbox-bugfix-pipeline"
  ]
}
```

Then in Claude Code, run: `/install-plugin bugfix-pipeline`

### Option 2: Direct file copy

Copy the `skills/` and `commands/` directories into your existing plugin cache or `~/.claude/skills/`.

## Usage

### Via slash command

```
/fix-sentry 6704185624
```

### Via natural language

```
Check Sentry issue 6704185624, fix it, create a ClickUp task, and make a PR.
```

### Via skill invocation

The skill `bugfix-pipeline:sentry-to-pr` is automatically available and will be suggested when you mention Sentry issues.

## Conventions

| Convention | Format |
|---|---|
| Branch naming | `bugfix/<CustomID>-<short-kebab-description>` |
| Commit prefix | `[b] [<CustomID>] <description>` |
| PR title | `[b] [<CustomID>] <ClickUp Task Title>` |
| ClickUp task name | `[Feature] [Screen] Bug description` |

## Customization

### Project-specific conventions

Add to your project's `CLAUDE.md`:

```markdown
## Git Remotes
- `origin`: your fork
- `upstream`: main repository (PR target)
```

### ClickUp configuration

The plugin asks the developer to choose the workspace, space, and sprint/folder before creating a task — no assumptions are made.

## Plugin Structure

```
sbox-bugfix-pipeline/
├── .claude-plugin/
│   └── marketplace.json    # Plugin metadata
├── skills/
│   └── sentry-to-pr/
│       └── SKILL.md        # Main workflow skill
├── commands/
│   └── fix-sentry.md       # /fix-sentry slash command
└── README.md
```

## Roadmap

| Skill | Description |
|---|---|
| `fix-from-clickup` | Read bug details directly from a ClickUp task, understand the issue context, and fix the bug — skipping Sentry investigation |
| `fix-from-email` | Parse bug reports from email, create a ClickUp task for tracking, then investigate and fix the bug |

## Contributing

1. Fork this repository
2. Create a feature branch
3. Follow the workflow conventions in the skill itself
4. Submit a PR

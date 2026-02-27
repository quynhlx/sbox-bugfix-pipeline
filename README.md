# Bugfix Pipeline Plugin

A Claude Code plugin that automates the complete bug fix lifecycle:

**Sentry Issue** → **Root Cause Analysis** → **Code Fix** → **ClickUp Task** → **Branch & PR**

## What it does

When you encounter a Sentry error, this plugin guides Claude through:

1. **Investigating** the Sentry issue (error details, stacktrace, impact)
2. **Finding the root cause** in the codebase
3. **Fixing** the bug with a minimal, targeted change
4. **Creating a ClickUp task** in the latest sprint with full context
5. **Creating a branch and PR** with proper naming, commit messages, and linked references

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
git clone <repo-url> ~/Sources/bugfix-pipeline

# Register as a local marketplace in Claude Code settings
# Add to ~/.claude/settings.json:
{
  "pluginMarketplaces": [
    "file:///Users/<you>/Sources/bugfix-pipeline"
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

## Customization

### Project-specific conventions

Add to your project's `CLAUDE.md`:

```markdown
## PR Naming Convention
- Bug fixes: `[b] [TICKET-ID] Description`

## Git Remotes
- `origin`: your fork
- `upstream`: main repository (PR target)
```

### ClickUp configuration

The plugin auto-discovers your ClickUp workspace, space, and sprint folder. If your sprint folder has a different name, update the skill file.

## Plugin Structure

```
bugfix-pipeline/
├── .claude-plugin/
│   └── marketplace.json    # Plugin metadata
├── skills/
│   └── sentry-to-pr/
│       └── SKILL.md        # Main workflow skill
├── commands/
│   └── fix-sentry.md       # /fix-sentry slash command
└── README.md
```

## Contributing

1. Fork this repository
2. Create a feature branch
3. Follow the workflow conventions in the skill itself
4. Submit a PR

# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Claude Code plugin** (not a traditional software project) that automates the bug fix lifecycle:

**Sentry Issue → Root Cause Analysis → Code Fix → ClickUp Task → Branch & PR**

It consists entirely of Markdown skill/command definitions — there is no compiled code, no package manager, no build system, and no test suite.

## Architecture

- **`commands/fix-sentry.md`** — Slash command (`/fix-sentry <issue-id>`) that delegates to the skill. Uses `disable-model-invocation: true` to pass control directly.
- **`skills/sentry-to-pr/SKILL.md`** — The core workflow. A 5-step linear pipeline that orchestrates MCP tool calls, codebase search, file edits, and CLI commands.
- **`.claude-plugin/marketplace.json`** — Plugin metadata for Claude Code's plugin marketplace system.

## External Dependencies

This plugin requires MCP servers and CLI tools configured **outside** this repo:

| Dependency | Purpose | Configuration |
|---|---|---|
| `@sentry/mcp-server` | Issue investigation, stacktrace analysis | `~/.claude/settings.json` with `SENTRY_AUTH_TOKEN` |
| `clickup-mcp-server` | Sprint task creation | `~/.claude/settings.json` with `CLICKUP_API_TOKEN` |
| `gh` (GitHub CLI) | PR creation | Must be authenticated via `gh auth login` |

## Modifying the Plugin

- **To change workflow steps**: Edit `skills/sentry-to-pr/SKILL.md`. The steps are numbered 1-5 and self-contained.
- **To change the slash command**: Edit `commands/fix-sentry.md`. The `$ARGUMENTS` placeholder passes user input to the skill.
- **To add new commands**: Create a new `.md` file in `commands/` with YAML frontmatter (`description`, optionally `disable-model-invocation`).
- **To add new skills**: Create a new directory under `skills/` with a `SKILL.md` file containing YAML frontmatter (`name`, `description`).

## Conventions

- Branch naming: `bugfix/<ClickUpID>-<short-kebab-description>`
- Commit prefix: `[b] [<ClickUpID>]`
- PR title format: `[b] [<ClickUpID>] <ClickUp Task Title>`
- ClickUp task name format: `[Feature] [Screen] Bug description`
- ClickUp tasks tagged with `["bug", "sentry"]`, priority `2` (high)
- Co-Authored-By uses the logged-in GitHub email (`git config user.email`)
- PR body must link both Sentry issue and ClickUp task
- After PR creation, ClickUp task status is updated to **PR - IN REVIEW**

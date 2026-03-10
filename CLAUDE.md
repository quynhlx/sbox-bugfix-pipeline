# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Claude Code plugin** (not a traditional software project) that automates the bug fix lifecycle:

**Sentry Issue → Root Cause Analysis → Dev Confirmation → ClickUp Task → Branch → Code Fix → Commit → Dev Review → PR → Status Update**

It consists entirely of Markdown skill/command definitions — there is no compiled code, no package manager, no build system, and no test suite.

## Architecture

- **`commands/fix-sentry.md`** — Slash command (`/fix-sentry <issue-id>`) for Sentry errors. Investigates error, creates ClickUp task, fixes and PRs.
- **`commands/fix-clickup.md`** — Slash command (`/fix-clickup <task-id-or-url>`) for existing ClickUp tasks. Investigates, fixes and PRs.
- **`skills/sentry-to-pr/SKILL.md`** — Full lifecycle from Sentry error: investigate → root cause → ClickUp task → fix → PR → update status.
- **`skills/clickup-to-pr/SKILL.md`** — Full lifecycle from existing ClickUp task: analyze → investigate → fix → PR → update status.
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

- Branch naming: `bugfix/<CustomID>-<short-kebab-description>` (CustomID = ClickUp custom task ID)
- Commit prefix: `[b] [<CustomID>]`
- PR title format: `[b] [<CustomID>] <ClickUp Task Title>`
- ClickUp task name format: `[Feature] [Screen] Bug description`
- ClickUp tasks tagged with `["bug", "sentry"]`, priority `2` (high)
- PR body must link both Sentry issue and ClickUp task
- After PR creation, ClickUp task status is updated to **PR - IN REVIEW**

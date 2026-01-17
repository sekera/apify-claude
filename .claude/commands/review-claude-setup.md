---
allowed-tools: Read, Write, Edit, Glob, Grep, Task, mcp__apify__search-apify-docs, mcp__apify__fetch-apify-docs
description: Review and fix Claude Code configuration issues
---

# Review Claude Code Setup

Comprehensive review of Claude Code configuration files for this project. Auto-fixes issues and reports changes.

## Documentation Sources

Before reviewing, consult these sources for best practices:

**Claude Code Documentation:**
- Use `Task` tool with `subagent_type: claude-code-guide` to research Claude Code best practices
- Topics: slash commands, settings, hooks, MCP servers, agents

**Apify Documentation:**
- Use MCP tools: `search-apify-docs` and `fetch-apify-docs`
- Or reference `.claude/skills/apify-ops/` for platform operations
- Topics: Actor development, schemas, deployment, Crawlee patterns

## Files to Review

- `CLAUDE.md` - Project instructions
- `.claude/settings.json` - Project settings
- `.claude/settings.local.json` - Local settings (if exists)
- `.claude/commands/*.md` - Custom commands/skills
- `.mcp.json` - MCP server configuration
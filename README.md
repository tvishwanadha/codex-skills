# Codex Review Skill

A skill that integrates OpenAI Codex as an MCP server for plan review, code review, and completion verification.

This repository is a [Claude Code plugin marketplace](https://code.claude.com/docs/en/plugin-marketplaces) containing an [agentskills.io](https://agentskills.io) compatible skill. The installation instructions below are for Claude Code, but the skill can be adapted for other compatible agents.

## What It Does

The `codex-review` skill teaches an agent to:
- **Review plans** before implementation
- **Verify completion** after implementation, especially after context compactions
- **Review code** after writing significant changes
- **Iterate on feedback** until Codex approves

## Prerequisites

- [Claude Code](https://claude.com/claude-code) installed
- [Codex CLI](https://github.com/openai/codex) installed

## Installation (Claude Code)

### 1. Install the Plugin

Add this repository as a marketplace and install the plugin:

```
/plugin marketplace add tvishwanadha/codex-skills
/plugin install codex-review
```

The plugin automatically configures the Codex MCP server.

### 2. Configure Claude to Use the Skill (Recommended)

Add the following to `~/.claude/CLAUDE.md`:

```markdown
# User-Level Instructions

## Using Codex for Review

**You MUST invoke the `codex-review` skill:**
- Before exiting plan mode for multi-file changes or architectural decisions
- Before telling the user work is complete after long tasks or context compactions
- After writing significant code changes

Skip only for: simple docs, single-file changes, trivial fixes.
```

### 3. Auto-approve Codex Tools

Add to `~/.claude/settings.json`:

```json
{
  "permissions": {
    "allow": ["mcp__plugin_codex-review_codex__*"]
  }
}
```

## How It Works

The plugin bundles an MCP server configuration that runs `codex mcp-server`. When installed, Claude Code namespaces the server as `plugin:codex-review:codex`, which determines the tool names:

- `mcp__plugin_codex-review_codex__codex` - Start a session
- `mcp__plugin_codex-review_codex__codex-reply` - Continue a session

The skill uses semantic tool references (e.g., "Codex MCP server's `codex` tool") rather than Claude Code-specific names, making SKILL.md portable across agents that support the [agentskills.io](https://agentskills.io) format.

## Compatibility

| Layer | Format | Reference |
|-------|--------|-----------|
| Distribution | Claude Code plugin marketplace | [plugin-marketplaces](https://code.claude.com/docs/en/plugin-marketplaces) |
| Skill | agentskills.io specification | [specification.md](https://agentskills.io/specification.md) |

The SKILL.md frontmatter includes `license` and `compatibility` fields per the agentskills.io spec.

## Skill Details

See [SKILL.md](plugins/codex-review/skills/codex-review/SKILL.md) for the full skill reference including:
- Constraints and limitations
- When to use (and skip)
- Workflow templates for plan review, completion verification, and code review
- Session management

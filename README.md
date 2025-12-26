# Codex Review Skill

A Claude Code skill that integrates OpenAI Codex as an MCP server for plan review, code review, and completion verification.

## What It Does

The `codex-review` skill teaches Claude to:
- **Review plans** before implementation
- **Verify completion** after implementation, especially after context compactions
- **Review code** after writing significant changes
- **Iterate on feedback** until Codex approves

## Prerequisites

- [Claude Code](https://claude.com/claude-code) installed
- [Codex CLI](https://github.com/openai/codex) installed

## Installation

### 1. Add Codex as an MCP Server

```bash
claude mcp add --scope user --transport stdio codex -- codex mcp-server
```

Verify it's connected:

```bash
claude mcp list
```

You should see:
```
codex: codex mcp-server - ✓ Connected
```

### 2. Install the Skill

Add this repository as a marketplace and install the skill:

```
/plugin marketplace add tvishwanadha/codex-skills
/plugin install codex-review
```

### 3. Configure Claude to Use the Skill (Recommended)

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

### 4. Auto-approve Codex Tools (Optional)

To avoid permission prompts for Codex tools, add to `~/.claude/settings.json`:

```json
{
  "permissions": {
    "allow": ["mcp__codex__*"]
  }
}
```

## Skill Details

See [SKILL.md](plugins/codex-review/skills/codex-review/SKILL.md) for the full skill reference including:
- Constraints and limitations
- When to use (and skip)
- Workflow templates for plan review, completion verification, and code review
- Session management

---
name: codex-review
description: Use Codex MCP for plan review, completion verification, and code review on multi-file changes or after context compactions.
license: MIT
compatibility: Requires Codex CLI with MCP server (`codex mcp-server`)
---

# Codex Review

## Constraints

- **Do not set the model parameter** - the user has already configured their preferred default
- **Project-only file access** - can only read files in the current project
- **Read-only reviewer** - provides feedback, you make the changes

If content is outside the project, inline it in your prompt.

## When to Use

**Use Codex for:**
- Multi-file implementation plans
- Architectural decisions or complex refactors
- Completion verification after context compactions

**Skip for:** simple docs, single-file changes, trivial fixes.

## Thread Configuration

When starting a thread with the `codex` tool, configure these parameters:

**approval-policy** - Controls shell command approval:
- `never` - **Always use this.** Claude and Codex don't support interactive approval
  flows properly - other options will cause the thread to hang.

**sandbox** - Controls filesystem and network access:
- `read-only` - Read files only, no writes/commands/network (safest for pure review)
- `workspace-write` - Read/write in workspace, run commands, optionally enable network
  (use if Codex needs to run tests or fetch dependencies)
- `danger-full-access` - No sandbox, no approvals (not recommended)

**Important:** These cannot be changed after starting a thread.

**Recommended defaults:**
- `approval-policy: never` (required - other values break)
- `sandbox: read-only` for pure review tasks
- `sandbox: workspace-write` if Codex needs to run tests or access network

## Process

All workflows follow this loop:

1. **Send request** using the Codex MCP server's `codex` tool (first call) or `codex-reply` tool (follow-ups)
2. **Receive feedback** from Codex
3. **Address feedback** - make changes or push back with reasoning
4. **Re-request review** until approved

Expect multi-minute responses; don't cancel early. Not all feedback must be incorporated - push back on overly pedantic points with reasoning.

## Workflows

### Plan Review

Use before exiting plan mode.

```
Context: [user's request and goals]
Scope: [in/out of scope]
Constraints: [requirements]
Plan: [path or inline content]

Review for completeness and correctness. Respond APPROVED if solid.
```

### Completion Verification

Use before telling the user work is complete, especially after:
- Context compaction during implementation
- Multi-file or multi-step tasks
- Long conversations

```
Implementation complete. Examine files on disk and verify:
- All planned changes were made
- No partial implementations remain
- No steps discussed but not executed

List anything incomplete.
```

If incomplete, fix and re-verify.

### Code Review

Use after writing significant code.

```
Purpose: [what the code should do]
Files: [paths or inline content]
Concerns: [specific areas to check]
Test results: [if available]

Review for correctness and issues.
```

## Thread Management

**Start thread**: Call the Codex MCP server's `codex` tool with your prompt and configuration.
The response includes:
- `content`: Codex's response
- `threadId`: Save for follow-ups

**Continue thread**: Use the `codex-reply` tool with:
- `threadId`: The thread ID from the previous response
- `prompt`: Your follow-up message

**Surviving context compaction**: Store thread info in your plan file:
```
## Active Codex Thread
- threadId: <id>
- sandbox: read-only
- purpose: plan review / code review / completion verification
```
This ensures you can resume the thread after compaction.

**Lost thread ID**: If not stored in plan file, start a fresh thread with context recovery info.

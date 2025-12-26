---
name: codex-review
description: Use Codex MCP for plan review, completion verification, and code review on multi-file changes or after context compactions.
---

# Codex Review

## Constraints

- **Do not set the model parameter** - the user has already configured their preferred default
- **No network access** - cannot fetch URLs or make API calls
- **Project-only file access** - can only read files in the current project
- **Read-only** - provides feedback, you make the changes

If content is outside the project, inline it in your prompt.

## When to Use

**Use Codex for:**
- Multi-file implementation plans
- Architectural decisions or complex refactors
- Completion verification after context compactions

**Skip for:** simple docs, single-file changes, trivial fixes.

## Process

All workflows follow this loop:

1. **Send request** via `mcp__codex__codex` (first call) or `mcp__codex__codex-reply` (follow-ups)
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

## Session Management

**Start session** - include in `mcp__codex__codex`:
```
Handshake token: <UNIQUE_TOKEN>

Find your session_id:
- Session files: ~/.codex/sessions/YYYY/MM/DD/
- Find .jsonl containing this token
- Session_id in first line's payload.id
- Return: SESSION_ID: <id>

[Your request...]
```

**Continue session** - use `mcp__codex__codex-reply` with the SESSION_ID.

**Recover lost session** - start fresh with context:
```
Handshake token: <UNIQUE_TOKEN>
[handshake instructions]

Context recovery:
- Plan: [path]
- Request: [summary]
- State: [in progress / verifying]

[continue review / verify completion]
```

# Memory & Context Management Knowledge Base

TRIGGER: memory, context, compact, compaction, session, persist, remember, forget, context window

## Overview

Claude Code has limited context windows. When approaching capacity (~95%), auto-compaction summarizes the conversation. This knowledge base provides strategies for preserving critical context across compaction events and sessions.

## How Compaction Works

### Trigger Points
- **Auto-compact**: Triggered at ~95% context capacity
- **Manual compact**: User runs `/compact` command
- **Clear**: User runs `/clear` to reset conversation

### What Happens During Compaction
1. Claude analyzes entire conversation history
2. Creates concise summary of:
   - What was accomplished
   - Current work in progress
   - Files involved
   - Next steps
   - Key user constraints
3. Summary replaces old conversation messages
4. Session continues with preserved context

### What Survives Compaction
- CLAUDE.md files (loaded fresh each session)
- MEMORY.md (your task registry)
- workspace/[task-id]/context.md (task-specific state)
- Git history
- All files on disk

### What Does NOT Survive
- Detailed conversation history (summarized but lost)
- Working memory of file contents (needs re-reading)
- Agent's "knowledge" of discussed topics
- Nuanced instructions from early in conversation

## Memory Hierarchy

Claude Code loads memories in order of precedence:

| Level | Location | Scope | Loaded |
|-------|----------|-------|--------|
| **Enterprise** | Org-wide policy | All teams | Always |
| **Project** | `./CLAUDE.md` | Team/shared | Always |
| **User** | `~/.claude/CLAUDE.md` | Your projects | Always |
| **Local** | `./CLAUDE.local.md` | Personal | Always |
| **Task** | `workspace/[task-id]/context.md` | Per-task | On access |

## Persistence Strategies

### 1. CLAUDE.md Optimization

**Keep it minimal but reference-rich**:
```markdown
# Project: Name

## Critical Rules
[Only absolute non-negotiables]

## Quick Reference
[One-liner for each key concept]

## File Structure
[Where to find things]

## Import Extended Context
@./agents/_orchestrator.md (for routing)
@./knowledge/organization.md (for workspace)
```

**Anti-patterns**:
- Don't include entire documentation
- Don't include code examples (put in knowledge/)
- Don't include conversation history

### 2. MEMORY.md Structure

**Essential sections that aid context recovery**:

```markdown
# System Memory

## Active Tasks
[What's currently being worked on - updated constantly]

## Agent Registry
[Quick lookup of all agents]

## Documentation Registry
[Quick lookup of all knowledge files]

## Session Summary
[Updated after significant milestones]

## Last Updated
[Timestamp for staleness detection]
```

### 3. Per-Task Context (workspace/[task-id]/context.md)

**Critical for compaction recovery**:

```markdown
# Task: [ID]

## Quick Resume
[1-2 sentences: What is this task and current status]

## Status: [ACTIVE/BLOCKED/COMPLETE]

## Key Files
[List of relevant files for quick re-reading]

## Agent Contributions
[What each agent discovered/produced]

## Next Steps
[What to do when resuming]
```

### 4. Compaction-Friendly Updates

**After each significant milestone**:
1. Update task context.md with current state
2. Ensure Next Steps are current

**This ensures that after compaction, you can**:
1. Read CLAUDE.md (always loaded)
2. List workspace/ folders for active tasks
3. Read relevant task context.md
4. Resume with full understanding

## Custom Compaction Hints

Guide compaction with specific instructions:

```
/compact preserve the agent decisions and task status
/compact keep the architectural decisions we made
/compact focus on the current implementation plan
/compact prioritize the bug fix we're working on
```

## Session Start Recovery Protocol

When starting a new session or recovering from compaction:

```
1. Read CLAUDE.md (automatic)
2. Read MEMORY.md to find active tasks
3. For each active task:
   - Read workspace/[task-id]/context.md
   - Re-read key files mentioned
4. Resume from recorded "Next Steps"
```

## Hooks for Memory Management

### PreCompact Hook

Runs before compaction happens:

```json
{
  "hooks": {
    "PreCompact": [
      {
        "matcher": "auto",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Compaction triggered at $(date)' >> .claude/compaction-log.txt"
          }
        ]
      }
    ]
  }
}
```

**Use cases**:
- Log compaction events
- Backup current state
- Ensure MEMORY.md is current

### SessionStart Hook

Runs when session starts or resumes:

```json
{
  "hooks": {
    "SessionStart": [
      {
        "matcher": "resume",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Session resumed at $(date)'"
          }
        ]
      }
    ]
  }
}
```

## Best Practices Summary

### DO
1. Keep CLAUDE.md lean (< 500 lines)
2. Update task context.md after each milestone
3. Use MEMORY.md as an index, not a log
4. Structure context.md for quick resume
5. Record Next Steps explicitly
6. Include file paths in all notes

### DON'T
1. Store conversation history in files
2. Include full code in CLAUDE.md
3. Rely on conversation memory for critical info
4. Forget to update task status
5. Use vague descriptions ("the thing we discussed")

## Quick Reference: Context Recovery Checklist

When recovering from compaction or new session:

- [ ] CLAUDE.md loads automatically
- [ ] List workspace/ folders to find active tasks
- [ ] Read context.md for each active task
- [ ] Re-read files mentioned in Key Files
- [ ] Review Next Steps for each task
- [ ] Continue from documented state

## Compaction Warning Signs

Take action when you notice:
- Claude forgetting earlier decisions
- Need to repeat instructions
- Claude re-reading files it already read
- Confusion about project structure

**Response**: Manually `/compact` with hints rather than waiting for auto-compact.

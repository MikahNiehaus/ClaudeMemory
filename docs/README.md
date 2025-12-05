# ClaudeMemory Documentation

> Auto-generated documentation for the multi-agent orchestration system.

## Quick Links

- [System Overview](#system-overview)
- [Agents](./agents/) - 13 specialist agents
- [Knowledge Bases](./knowledge/) - 17 knowledge bases
- [Commands](./commands/) - 5 slash commands

## System Overview

Multi-agent orchestration system where Claude acts as lead agent, delegating to specialized subagents. Agents collaborate through per-task context files.

### Capabilities

| Category | Count | Description |
|----------|-------|-------------|
| Agents | 13 | Specialist subagents for different domains |
| Knowledge Bases | 17 | Reference documentation for agents |
| Slash Commands | 5 | Workflow automation commands |
| Hooks | 3 | SessionStart, PreCompact, Task validation |

### Architecture

```
User Request
    │
    ▼
┌─────────────────┐
│  Orchestrator   │  ← CLAUDE.md (routes requests)
│  (Lead Agent)   │
└────────┬────────┘
         │
    ┌────┴────┐
    ▼         ▼
┌───────┐ ┌───────┐
│Agent A│ │Agent B│  ← Specialist agents
└───┬───┘ └───┬───┘
    │         │
    └────┬────┘
         ▼
┌─────────────────┐
│ workspace/      │  ← Per-task context
│ [task-id]/      │
│ context.md      │
└─────────────────┘
```

## Getting Started

1. **Start a session**: Claude reads CLAUDE.md automatically
2. **Make a request**: Describe what you need
3. **Agents are spawned**: Orchestrator delegates to specialists
4. **Results synthesized**: Combined response returned

## File Structure

```
ClaudeMemory/
├── CLAUDE.md           # Orchestrator instructions
├── MEMORY.md           # System registry & state
├── .claude/
│   ├── settings.json   # Permissions & hooks
│   └── commands/       # Slash commands
├── agents/             # 13 agent definitions
├── knowledge/          # 17 knowledge bases
├── workspace/          # Per-task work folders
└── docs/               # This documentation
```

---
*Last updated: 2025-12-05*

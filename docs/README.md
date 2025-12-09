# Claude Multi-Agent Orchestration System Documentation

> Auto-generated documentation. Run `/update-docs` to refresh.

**Last Updated**: 2025-12-09

## System Overview

Multi-agent orchestration system where Claude acts as lead agent, delegating to specialized subagents. Agents collaborate through per-task context files.

| Component | Count |
|-----------|-------|
| Specialist Agents | 14 |
| Knowledge Bases | 19 |
| Slash Commands | 6 |

## Quick Links

- [Agents](./agents/README.md) - Specialist agent definitions and capabilities
- [Knowledge Bases](./knowledge/README.md) - Domain expertise documentation
- [Commands](./commands/README.md) - Slash command reference
- [Architecture](./architecture/) - System design documents

## How It Works

1. **User Request** arrives at the Lead Agent (orchestrator)
2. **Orchestrator analyzes** the request and determines which specialists are needed
3. **Agents are spawned** with full context (agent definition + knowledge base + task context)
4. **Agents collaborate** through per-task context files in `workspace/[task-id]/`
5. **Results are synthesized** and returned to the user

## Getting Started

### For Users
- Make requests naturally - the orchestrator will delegate appropriately
- Use `/list-agents` to see available specialists
- Use `/spawn-agent <name> <task-id>` for explicit agent invocation

### For Contributors
- See [MEMORY.md](../MEMORY.md) for system state and registry
- See [CLAUDE.md](../CLAUDE.md) for orchestrator instructions
- Follow the "Adding New Components" section in MEMORY.md

## File Structure

```
ClaudeMemory/
├── CLAUDE.md              # Orchestrator instructions
├── MEMORY.md              # System state & registry
├── agents/                # 14 agent definitions
├── knowledge/             # 19 knowledge bases
├── workspace/             # Per-task work areas
├── .claude/commands/      # 6 slash commands
└── docs/                  # This documentation
```

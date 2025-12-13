# Claude Multi-Agent Orchestration System

A sophisticated multi-agent orchestration system for Claude Code that transforms Claude into a lead agent capable of delegating to 16 specialized subagents for complex software engineering tasks.

## System Stats

| Component | Count |
|-----------|-------|
| Specialist Agents | 16 |
| Knowledge Bases | 25 |
| Machine-Readable Rules | 13 |
| Slash Commands | 9 |

## Quick Start

1. **Clone/copy this system** into your project
2. **Start Claude Code** - it will automatically load `CLAUDE.md`
3. **Give Claude a task** - the orchestrator will:
   - Analyze what's needed
   - Create a task workspace
   - Spawn appropriate agents
   - Coordinate their work
   - Synthesize results

## Key Features

### Multi-Agent Architecture
- **Lead Agent (Orchestrator)**: Claude analyzes requests and delegates to specialists
- **18 Specialist Agents**: Each expert in a specific domain (testing, debugging, security, etc.)
- **Per-Task Context**: Each task gets isolated context in `workspace/[task-id]/`
- **Handoff Protocol**: Agents pass findings through context.md files

### Machine-Readable Rules
14 rules encoded with TRIGGER, CONDITION, ACTION, SEVERITY for consistent enforcement:
- RULE-001: Agent Spawn Required for Code Changes
- RULE-002: TodoWrite for Multi-Step Tasks
- RULE-003: Planning Phase Required
- RULE-012: Self-Reflection Required
- And more...

### Knowledge System
30 knowledge bases covering:
- Software engineering best practices
- Security (OWASP Top 10)
- Testing & TDD
- Architecture patterns
- Performance optimization
- And more...

### Self-Reflection Protocol
All agents must self-reflect before finalizing output:
- Task alignment check
- Assumption verification
- Error analysis
- Confidence scoring (HIGH/MEDIUM/LOW)

## Project Structure

```
ClaudeMemory/
├── CLAUDE.md              # Orchestrator instructions (loaded every session)
├── .claude/
│   ├── settings.json      # Permissions, hooks, sandbox config
│   └── commands/          # 9 slash commands
├── agents/                # 18 specialist agents + orchestrator
├── knowledge/             # 30 knowledge bases
├── workspace/             # Per-task work folders
└── docs/                  # This documentation (gitignored)
```

## Available Agents

| Agent | Expertise |
|-------|-----------|
| test-agent | Testing, TDD, coverage |
| debug-agent | Bug analysis, root cause |
| architect-agent | Design, SOLID, patterns |
| security-agent | OWASP, vulnerability review |
| reviewer-agent | Code review, PR feedback |
| docs-agent | Documentation writing |
| ui-agent | Frontend implementation |
| workflow-agent | Complex task execution |
| research-agent | Web research, verification |
| refactor-agent | Code smells, cleanup |
| explore-agent | Codebase exploration |
| performance-agent | Profiling, optimization |
| ticket-analyst-agent | Requirements clarification |
| compliance-agent | Rule auditing |
| browser-agent | Interactive browser testing |
| estimator-agent | Story pointing |

## Slash Commands

| Command | Description |
|---------|-------------|
| `/spawn-agent <agent> <task-id>` | Spawn agent with validation |
| `/agent-status <task-id>` | Display task status |
| `/list-agents` | List all available agents |
| `/check-task <task-id>` | Validate task folder |
| `/plan-task <task-id> <desc>` | Execute planning phase |
| `/set-mode <normal\|persistent>` | Set execution mode |
| `/check-completion [task-id]` | Verify completion criteria |
| `/compact-review` | Preview state before compaction |
| `/update-docs` | Regenerate this documentation |

## Execution Modes

### NORMAL Mode (Default)
- Stop after each step
- Check with user before proceeding
- Good for interactive work

### PERSISTENT Mode
- Continue automatically until completion criteria met
- Auto-checkpointing for recovery
- Good for large refactoring/migration tasks

## Documentation

- [Architecture](./architecture.md) - System design and patterns
- [Agents Reference](./agents.md) - Complete agent documentation
- [Knowledge Bases](./knowledge-bases.md) - Domain expertise reference
- [Rules Reference](./rules.md) - Machine-readable rules

## Research Basis

This system incorporates best practices from:
- [Anthropic Multi-Agent Research System](https://www.anthropic.com/engineering/multi-agent-research-system)
- [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices)
- Academic research on LLM self-reflection and anti-hallucination
- LangGraph/AutoGen memory management patterns

## Version

- **Last Updated**: 2025-12-12
- **Agents**: 16
- **Knowledge Bases**: 25
- **Rules**: 13
- **Commands**: 9

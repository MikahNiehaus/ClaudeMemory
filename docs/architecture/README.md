# System Architecture

Complete architectural documentation for the ClaudeMemory multi-agent orchestration system.

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        USER REQUEST                              │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                     ORCHESTRATOR (CLAUDE.md)                     │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐              │
│  │   Analyze   │→ │   Route     │→ │  Coordinate │              │
│  │   Request   │  │   to Agent  │  │   Results   │              │
│  └─────────────┘  └─────────────┘  └─────────────┘              │
└─────────────────────────────┬───────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
        ▼                     ▼                     ▼
┌───────────────┐    ┌───────────────┐    ┌───────────────┐
│  AGENT POOL   │    │   KNOWLEDGE   │    │   WORKSPACE   │
│  (13 agents)  │    │   (17 bases)  │    │  (per-task)   │
└───────────────┘    └───────────────┘    └───────────────┘
```

## Core Components

### 1. Orchestrator Layer
**File**: `CLAUDE.md`

Responsibilities:
- Analyze incoming requests
- Determine required expertise
- Route to appropriate agent(s)
- Coordinate multi-agent collaboration
- Synthesize final results

Decision Flow:
```
Request → Domain Analysis → Agent Selection → Spawn → Coordinate → Respond
```

### 2. Agent Layer
**Directory**: `agents/`

13 specialist agents, each with:
- **Role**: What the agent does
- **Goal**: What it aims to achieve
- **Backstory**: Context and expertise
- **Capabilities**: Specific skills
- **Collaboration Protocol**: How it works with other agents
- **Output Format**: Structured response format

### 3. Knowledge Layer
**Directory**: `knowledge/`

17 knowledge bases providing:
- Domain expertise
- Best practices
- Checklists
- Code examples
- Decision frameworks

### 4. Workspace Layer
**Directory**: `workspace/`

Per-task isolation:
```
workspace/[task-id]/
├── context.md    # Task state, agent handoffs
├── mockups/      # Input materials
├── outputs/      # Generated artifacts
└── snapshots/    # Progress captures
```

### 5. Configuration Layer
**Directory**: `.claude/`

- `settings.json`: Permissions, hooks, sandbox
- `commands/`: 6 slash commands

## Data Flow

### Single Agent Flow
```
User Request
    │
    ▼
Orchestrator analyzes
    │
    ▼
Spawns single agent with:
├── Agent definition
├── Knowledge base
└── Task instructions
    │
    ▼
Agent executes
    │
    ▼
Returns: COMPLETE/BLOCKED/NEEDS_INPUT
    │
    ▼
Orchestrator returns result
```

### Multi-Agent Sequential Flow
```
User Request (e.g., "fix bug and add tests")
    │
    ▼
Orchestrator creates workspace/[task-id]/
    │
    ▼
Spawns debug-agent
    │
    ▼
debug-agent → COMPLETE
    │
    ▼
Updates context.md with findings
    │
    ▼
Spawns test-agent (reads context.md)
    │
    ▼
test-agent → COMPLETE
    │
    ▼
Synthesizes combined result
```

### Multi-Agent Parallel Flow
```
User Request (e.g., "comprehensive PR review")
    │
    ▼
Orchestrator spawns in parallel:
├── reviewer-agent
├── test-agent
├── security-agent
└── architect-agent
    │
    ▼
All agents return independently
    │
    ▼
Orchestrator merges all feedback
```

## State Management

### Session State
- `MEMORY.md`: System registry, active tasks
- `workspace/[task-id]/context.md`: Per-task state

### Persistence Hooks
1. **SessionStart**: Auto-loads MEMORY.md
2. **PreCompact**: Preserves critical context
3. **Task Validation**: Enforces compliance

### Status Values

| Context | Status | Meaning |
|---------|--------|---------|
| Task | ACTIVE | Work in progress |
| Task | BLOCKED | Cannot proceed |
| Task | COMPLETE | Finished |
| Agent | COMPLETE | Success |
| Agent | BLOCKED | Stuck |
| Agent | NEEDS_INPUT | Needs user |

## Security Model

### Permission Tiers
1. **Allow**: Auto-approved (safe tools)
2. **Ask**: Requires confirmation (git writes, sensitive reads)
3. **Deny**: Blocked (none currently - user preference)

### Validation Hooks
- Task spawn validation ensures full context
- Edit/Write hooks check agent compliance

## Scalability Patterns

### Adding New Agent
1. Create `agents/[name]-agent.md`
2. Create `knowledge/[topic].md`
3. Update orchestrator routing
4. Update MEMORY.md registry
5. Run `/update-docs`

### Adding New Knowledge
1. Create `knowledge/[topic].md`
2. Add TRIGGER line
3. Update CLAUDE.md router
4. Update MEMORY.md registry
5. Run `/update-docs`

## Design Principles

1. **Per-task isolation**: No global state pollution
2. **Full context on spawn**: Agents get complete information
3. **Status-driven handoffs**: Clear COMPLETE/BLOCKED/NEEDS_INPUT
4. **Token efficiency**: Load only what's needed
5. **Graceful degradation**: Handle failures cleanly

---
*Last updated: 2025-12-05*

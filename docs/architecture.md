# System Architecture

## Overview

The Claude Multi-Agent Orchestration System implements a hierarchical multi-agent architecture where Claude acts as the lead agent (orchestrator), delegating to specialist subagents for domain-specific tasks.

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                        User Request                              │
└─────────────────────────────────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────┐
│                     Lead Agent (Orchestrator)                    │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐  │
│  │  CLAUDE.md  │  │  MEMORY.md  │  │  agents/_orchestrator   │  │
│  │  (rules)    │  │  (registry) │  │  (routing logic)        │  │
│  └─────────────┘  └─────────────┘  └─────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                               │
           ┌───────────────────┼───────────────────┐
           ▼                   ▼                   ▼
    ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
    │  Agent A    │     │  Agent B    │     │  Agent C    │
    │  (domain)   │     │  (domain)   │     │  (domain)   │
    └─────────────┘     └─────────────┘     └─────────────┘
           │                   │                   │
           └───────────────────┼───────────────────┘
                               ▼
                    ┌─────────────────────┐
                    │  workspace/[task]/  │
                    │  context.md         │
                    │  (shared state)     │
                    └─────────────────────┘
```

## Core Components

### 1. Lead Agent (Orchestrator)

The orchestrator is defined in `CLAUDE.md` and loaded every session. It:
- Analyzes incoming requests
- Determines which agents to spawn
- Coordinates sequential and parallel agent work
- Synthesizes results for the user

**Key files**:
- `CLAUDE.md` - Main instructions, rules, quick reference
- `agents/_orchestrator.md` - Detailed routing logic, collaboration matrix
- `MEMORY.md` - System registry, session history

### 2. Specialist Agents

16 agents with domain expertise:

| Category | Agents |
|----------|--------|
| Code Quality | test-agent, debug-agent, reviewer-agent, refactor-agent |
| Design | architect-agent, ui-agent |
| Security | security-agent |
| Documentation | docs-agent |
| Analysis | explore-agent, performance-agent, ticket-analyst-agent |
| Execution | workflow-agent, browser-agent |
| Research | research-agent |
| Compliance | compliance-agent |
| Estimation | estimator-agent |

### 3. Knowledge Bases

25 markdown files in `knowledge/` providing domain expertise:
- Agents read these for best practices
- Organized by topic with TRIGGER keywords
- Include checklists, examples, anti-patterns

### 4. Per-Task Context

Each task gets isolated context in `workspace/[task-id]/`:
```
workspace/[task-id]/
├── mockups/       # Input designs, references
├── outputs/       # Generated artifacts
├── snapshots/     # Screenshots, progress
└── context.md     # Task state, agent handoffs
```

## Key Patterns

### Orchestration Pattern

```
User Request
    │
    ├─ Read-only question? ──────────► Direct answer (no agents)
    │
    └─ Requires agents? ─────────────► PLANNING PHASE
            │
            ├─ Create task workspace
            ├─ Run Planning Checklist (7 domains)
            ├─ Generate plan in context.md
            │
            └─ EXECUTION PHASE
                 │
                 ├─ Single domain? ─────────► Spawn 1 agent
                 ├─ Multiple, dependent? ───► Sequential agents
                 └─ Multiple, independent? ─► Parallel agents
```

### Agent Collaboration Patterns

**Sequential Handoff**:
```
"Fix this bug and add tests"
→ debug-agent (root cause)
→ Update context.md
→ test-agent (regression tests)
→ Synthesize
```

**Parallel Analysis**:
```
"Review this PR"
→ Spawn in parallel:
  • reviewer-agent
  • test-agent
  • architect-agent
→ Merge feedback
```

### Token Efficiency Pattern

Agents READ files instead of receiving pasted content:
```markdown
## Your Role
You are test-agent. READ `agents/test-agent.md` for your full definition.

## Your Knowledge Base
READ `knowledge/testing.md` for domain expertise.
```

This saves ~2000 tokens per spawn (97% reduction) with identical quality.

## Rule System

13 machine-readable rules in `CLAUDE.md` with:
- **TRIGGER**: When to check the rule
- **CONDITION**: What must be true
- **ACTION**: What to do if violated
- **SEVERITY**: BLOCK or WARN

Key rules:
- RULE-001: Agent spawn required for code changes
- RULE-003: Planning phase required before agents
- RULE-012: Self-reflection required before COMPLETE

## State Management

### Session State
- `MEMORY.md` - System registry, component counts
- `workspace/` folders - Active task list

### Task State
- `workspace/[task-id]/context.md` - Per-task state
- Quick Resume section for compaction recovery
- Agent Contributions section for handoffs

### Execution Modes
- **NORMAL**: Stop after each step, check with user
- **PERSISTENT**: Continue until completion criteria met

## Self-Reflection Protocol

All agents must self-reflect before finalizing (RULE-012):
1. Task alignment check
2. Assumption verification
3. Error analysis
4. Confidence assessment (HIGH/MEDIUM/LOW with reasoning)

This improves accuracy and catches hallucinations.

## Model Selection

Different models for different tasks (RULE-013):
| Task Type | Model |
|-----------|-------|
| Complex reasoning | opus |
| Code review, research | sonnet |
| Quick lookups | haiku |

Priority: Accuracy > Speed > Token Cost

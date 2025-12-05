# Claude Multi-Agent Orchestration System

## Session Quick Start

**On new session or after compaction**:
1. This file (MEMORY.md) provides system state
2. Check Active Tasks below for current work
3. Read `workspace/[task-id]/context.md` for each active task
4. Resume from documented "Next Steps"

## System Overview

Multi-agent orchestration system where Claude acts as lead agent, delegating to specialized subagents. Agents collaborate through per-task context files.

**12 Specialist Agents** | **14 Knowledge Bases** | **Per-task context isolation**

## Architecture

```
ClaudeMemory/
├── CLAUDE.md              # Main orchestrator instructions (loaded every session)
├── MEMORY.md              # This file - system registry (check first!)
├── agents/                # Agent definitions
│   ├── _orchestrator.md   # Routing logic + collaboration matrix
│   └── [12 specialist agents]
├── workspace/             # Task-organized work area
│   └── [task-id]/         # Per-task folders
│       ├── mockups/       # Input designs, references
│       ├── outputs/       # Generated artifacts
│       ├── snapshots/     # Screenshots, progress
│       └── context.md     # Task context & agent handoffs
└── knowledge/             # Knowledge bases
    └── [14 documentation files]
```

## Agent Registry

| Agent | Role | Knowledge Base | Added |
|-------|------|----------------|-------|
| `test-agent` | Testing, TDD, coverage | `knowledge/testing.md` | 2025-12-04 |
| `debug-agent` | Bug analysis, root cause | `knowledge/debugging.md` | 2025-12-04 |
| `architect-agent` | Design, SOLID, patterns | `knowledge/architecture.md` | 2025-12-04 |
| `reviewer-agent` | PR review, feedback | `knowledge/pr-review.md` | 2025-12-04 |
| `docs-agent` | Documentation writing | `knowledge/documentation.md` | 2025-12-04 |
| `estimator-agent` | Story pointing | `knowledge/story-pointing.md` | 2025-12-04 |
| `ui-agent` | UI implementation | `knowledge/ui-implementation.md` | 2025-12-04 |
| `workflow-agent` | Execution coordination | `knowledge/workflow.md` | 2025-12-04 |
| `research-agent` | Web research, verification | `knowledge/research.md` | 2025-12-04 |
| `security-agent` | Security review, OWASP | `knowledge/security.md` | 2025-12-04 |
| `refactor-agent` | Code smells, refactoring | `knowledge/refactoring.md` | 2025-12-04 |
| `explore-agent` | Codebase exploration, understanding | `knowledge/code-exploration.md` | 2025-12-04 |

## Documentation Registry

| File | Domain | Triggers | Added |
|------|--------|----------|-------|
| `knowledge/testing.md` | Test Writing | test, TDD, mock, assert | 2025-12-04 |
| `knowledge/debugging.md` | Bug Fixing | debug, error, bug, fix | 2025-12-04 |
| `knowledge/documentation.md` | Code Documentation | document, docstring, README | 2025-12-04 |
| `knowledge/workflow.md` | Development Workflow | implement, execute, workflow | 2025-12-04 |
| `knowledge/story-pointing.md` | Story Estimation | estimate, story point, sprint | 2025-12-04 |
| `knowledge/architecture.md` | Software Architecture | architecture, SOLID, pattern | 2025-12-04 |
| `knowledge/pr-review.md` | PR Review | review, PR, pull request | 2025-12-04 |
| `knowledge/ui-implementation.md` | UI Implementation | UI, mockup, frontend, Figma | 2025-12-04 |
| `knowledge/organization.md` | Workspace Organization | organize, workspace, task folder, context | 2025-12-04 |
| `knowledge/research.md` | Web Research | research, search, verify, citation | 2025-12-04 |
| `knowledge/security.md` | Application Security | security, OWASP, vulnerability, XSS, injection | 2025-12-04 |
| `knowledge/refactoring.md` | Code Refactoring | refactor, code smell, technical debt, clean code | 2025-12-04 |
| `knowledge/api-design.md` | API Design | API, REST, endpoint, HTTP, versioning | 2025-12-04 |
| `knowledge/code-exploration.md` | Code Exploration | explore, codebase, understand, find, where, how does | 2025-12-04 |
| `knowledge/memory-management.md` | Memory & Context | memory, context, compact, compaction, session, persist | 2025-12-04 |

## Active Tasks

| Task ID | Description | Status | Workspace | Created |
|---------|-------------|--------|-----------|---------|
| (none)  | -           | -      | -         | -       |

*Register tasks here when starting multi-step work. See `knowledge/organization.md` for task folder guidelines.*

**Task Status Values**: ACTIVE, BLOCKED, COMPLETE

## Adding New Components

### New Agent
1. Create `agents/[name]-agent.md` using existing agent format:
   - Role, Goal, Backstory
   - Capabilities
   - Knowledge Base reference
   - Collaboration Protocol
   - Output Format (include Status field)
2. Add to Agent Registry above
3. Update `CLAUDE.md` Agent Roster
4. Update `agents/_orchestrator.md` routing table

### New Documentation
1. Create `knowledge/[topic].md` with:
   - TRIGGER line at top
   - Organized headers
   - Actionable checklists
   - Code examples where relevant
2. Add to Documentation Registry above
3. Update `CLAUDE.md` router table
4. Consider creating a matching agent

## Session History

- **2025-12-04**: Initial system setup
  - Created adaptive memory architecture
  - Converted 8 PDFs to documentation
  - Created router system

- **2025-12-04**: Multi-Agent Orchestration Upgrade
  - Created 8 specialist agent definitions
  - Added orchestrator routing logic
  - Implemented shared context for collaboration
  - Updated CLAUDE.md to orchestrator mode

- **2025-12-04**: Per-Task Context Architecture
  - Moved context from global to per-task
  - Merged notes.md + context.md into single file
  - Added actionable collaboration matrix to _orchestrator.md
  - Added BLOCKED/NEEDS_INPUT status handling
  - Removed shared/ folder (context now in workspace/)

- **2025-12-04**: Expanded Agent & Knowledge System
  - Renamed `docs/` folder to `knowledge/` (avoid confusion with documentation content)
  - Created `research-agent` with web research methodology (planner-executor-synthesizer pattern)
  - Created `security-agent` with OWASP Top 10 2025 coverage
  - Created `refactor-agent` with Martin Fowler's code smell catalog
  - Added `knowledge/research.md` - multi-source verification, anti-hallucination techniques
  - Added `knowledge/security.md` - vulnerability prevention, secure coding practices
  - Added `knowledge/refactoring.md` - code smells, refactoring techniques, technical debt
  - Added `knowledge/api-design.md` - REST principles, HTTP semantics, RFC 9457 errors

- **2025-12-04**: Memory/Compaction System & Explore Agent
  - Created `explore-agent` for codebase exploration and understanding
  - Added `knowledge/code-exploration.md` - systematic exploration methodology
  - Added `knowledge/memory-management.md` - compaction recovery, context persistence
  - Updated MEMORY.md with "Session Quick Start" section for compaction recovery
  - Total: 12 agents, 14 knowledge bases

## Notes

### Key Research Sources
- [Anthropic Multi-Agent System](https://www.anthropic.com/engineering/multi-agent-research-system)
- [wshobson/agents Architecture](https://github.com/wshobson/agents)
- [CrewAI Agent Patterns](https://docs.crewai.com/en/concepts/agents)

### Design Decisions
1. **LLM-based routing**: Claude decides which agent(s) to spawn based on analysis
2. **Per-task context**: Each task has isolated context in `workspace/[task-id]/context.md`
3. **Three-tier knowledge**: Agent metadata (always) → Full definition (on spawn) → Docs (on demand)
4. **Token efficiency**: Only load what's needed for each delegation
5. **Status-driven handoffs**: Agents report COMPLETE/BLOCKED/NEEDS_INPUT for clear coordination

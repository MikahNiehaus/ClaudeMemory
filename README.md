# Claude Multi-Agent Orchestration Toolkit

A plug-and-play toolkit that supercharges Claude Code with specialized AI agents. Copy this into any project to get automatic task delegation, rich context management, and multi-agent collaboration.

## Quick Start

1. **Copy into your project**:
   ```bash
   # Clone or copy these folders into your project root:
   # - .claude/          (settings, commands)
   # - agents/           (agent definitions)
   # - knowledge/        (domain expertise)
   # - workspace/        (task tracking)
   # - CLAUDE.md         (orchestrator instructions)
   # - MEMORY.md         (system registry)
   ```

2. **Start Claude Code** in your project - the system loads automatically

3. **Work normally** - Claude will delegate to specialists as needed

## What This Does

Instead of Claude handling everything itself, this toolkit makes it:

- **Spawn specialist agents** for testing, debugging, architecture, security, etc.
- **Track each issue/task** in its own folder with context and notes
- **Coordinate multi-agent workflows** (debug → test → review)
- **Survive context compaction** by persisting state to files

## The 14 Specialist Agents

| Agent | Expertise | Spawned For |
|-------|-----------|-------------|
| `test-agent` | TDD, coverage | Writing tests |
| `debug-agent` | Root cause analysis | Bug fixing |
| `architect-agent` | SOLID, patterns | Design decisions |
| `reviewer-agent` | Code quality | PR reviews |
| `docs-agent` | Documentation | Writing docs |
| `estimator-agent` | Story points | Ticket estimation |
| `ui-agent` | Frontend | UI implementation |
| `workflow-agent` | Execution | Complex implementations |
| `research-agent` | Web research | Fact-checking, learning |
| `security-agent` | OWASP, vulnerabilities | Security audits |
| `refactor-agent` | Code smells | Technical debt cleanup |
| `explore-agent` | Codebase understanding | Finding patterns |
| `performance-agent` | Profiling | Optimization |
| `ticket-analyst-agent` | Requirements | Clarifying vague requests |

## Memory & Documentation Flow

### While Working → `workspace/`

All active notes, discoveries, and context go in issue folders:

```
workspace/
├── ASC-914/                    # Ticket number as folder name
│   ├── context.md              # Notes, findings, agent handoffs
│   ├── mockups/                # Input designs, references
│   ├── outputs/                # Generated artifacts
│   └── snapshots/              # Screenshots, progress
│
└── 2025-12-10-fix-login/       # Or date-based for ad-hoc tasks
    └── context.md
```

**context.md tracks:**
- Task status (ACTIVE/BLOCKED/COMPLETE)
- Notes and findings as I work
- What I've discovered, what's next
- Agent contributions and handoffs
- Open questions

This is my working memory. It survives session resets and context compaction.

### When Done → `docs/`

After work is complete, run `/update-docs` to generate polished project documentation:

```
docs/
├── README.md           # Project overview
├── architecture.md     # How it's designed
├── api.md              # API reference
└── ...                 # Whatever the project needs
```

**docs/ describes the finished project** - clean, organized, for humans to read.

### The Flow

```
Working on issue    →    workspace/[issue]/context.md (scratchpad)
                              ↓
Work complete       →    /update-docs
                              ↓
                         docs/ (polished documentation)
```

## How Delegation Works

### Single Agent
```
"Write tests for the auth module"
→ test-agent handles it
```

### Sequential (Dependent)
```
"Fix this bug and add tests"
→ debug-agent (finds root cause)
→ saves findings to context.md
→ test-agent (writes regression tests)
```

### Parallel (Independent)
```
"Review this PR comprehensively"
→ reviewer-agent  ─┐
→ test-agent      ├─→ merged feedback
→ security-agent  ─┘
```

## File Structure

```
your-project/
├── .claude/
│   ├── settings.json      # Permissions (pre-configured)
│   └── commands/          # Slash commands (/spawn-agent, /update-docs, etc.)
├── agents/
│   ├── _orchestrator.md   # Routing logic
│   ├── _shared-output.md  # Common output format
│   └── [14 agent files]
├── knowledge/             # 19 domain expertise files
├── workspace/             # Your task folders go here
├── CLAUDE.md              # Orchestrator instructions (loaded every session)
├── MEMORY.md              # System registry
└── [your project files]
```

## Token Efficiency

This toolkit is optimized to minimize token usage:

- **Agents READ files** instead of receiving pasted content (~97% reduction)
- **Single source of truth** - no duplicated rosters
- **Lazy loading** - knowledge bases loaded only when needed

## Pre-Configured Permissions

### Auto-Allowed (No Prompts)
- File operations (Read, Write, Edit, Glob, Grep)
- Development tools (npm, node, python, cargo, go, etc.)
- Git workflow (add, commit, push, pull, fetch, merge, checkout)
- GitHub CLI (pr create, issue create)

### Requires Approval
- Destructive git (reset, clean, force push)
- System commands (sudo, curl, wget)
- Sensitive files (.env, secrets, credentials)

### Blocked
- `rm -rf /`, disk formatting, database drops
- System shutdown commands

Edit `.claude/settings.json` to customize.

## Slash Commands

| Command | Description |
|---------|-------------|
| `/spawn-agent <name> <task-id>` | Manually spawn an agent |
| `/list-agents` | Show all available agents |
| `/agent-status <task-id>` | Check task progress |
| `/check-task <task-id>` | Validate task folder |
| `/update-docs` | Generate documentation |
| `/compact-review` | Review state before compaction |

## Adding to the System

### New Agent
1. Create `agents/[name]-agent.md`
2. Add to `MEMORY.md` Agent Registry
3. Update `CLAUDE.md` Agent Roster

### New Knowledge Base
1. Create `knowledge/[topic].md`
2. Add to `MEMORY.md` Documentation Registry
3. Add to `CLAUDE.md` router table

## Design Sources

- [Anthropic Multi-Agent Research](https://www.anthropic.com/engineering/multi-agent-research-system)
- [CrewAI Agent Patterns](https://docs.crewai.com/en/concepts/agents)
- [Model Context Protocol](https://www.anthropic.com/news/model-context-protocol)

## Key Principles

1. **Agents for everything** - Even "simple" tasks get proper specialists
2. **Per-task isolation** - Each issue has its own context
3. **Status-driven** - Agents report COMPLETE/BLOCKED/NEEDS_INPUT
4. **Survives compaction** - State persists in files, not memory
5. **Token efficient** - Minimal overhead, maximum capability

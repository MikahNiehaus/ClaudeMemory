# Claude Multi-Agent Orchestration System

A multi-agent orchestration system where Claude automatically delegates tasks to specialized AI agents that can collaborate with each other.

## How It Works

1. **User Request** → Main Claude (orchestrator) analyzes the task
2. **LLM Routing** → Orchestrator decides which specialist agent(s) to spawn
3. **Delegation** → Agents receive role definition + knowledge base + task
4. **Collaboration** → Agents share context through `workspace/[task-id]/context.md`
5. **Synthesis** → Orchestrator combines results into unified response

## Available Agents

| Agent | Expertise | Use For |
|-------|-----------|---------|
| **test-agent** | Testing, TDD, coverage | Writing tests, test strategy |
| **debug-agent** | Bug analysis, root cause | Errors, debugging issues |
| **architect-agent** | Design, SOLID, patterns | Architecture decisions |
| **reviewer-agent** | Code review, feedback | PR reviews |
| **docs-agent** | Documentation | Writing docs, docstrings |
| **estimator-agent** | Story pointing | Ticket estimation |
| **ui-agent** | UI implementation | Frontend, mockups |
| **workflow-agent** | Execution planning | Complex implementations |
| **research-agent** | Web research, verification | Deep research, fact-checking |
| **security-agent** | Security review, OWASP | Vulnerability assessment, secure coding |
| **refactor-agent** | Code smells, refactoring | Code cleanup, technical debt |

## Delegation Patterns

### Single Agent
Simple, single-domain tasks spawn one specialist.
```
"Write tests for this function" → test-agent
```

### Sequential (Dependent Tasks)
Tasks build on each other through per-task context.
```
"Fix this bug and add tests"
→ debug-agent (finds root cause)
→ workspace/[task-id]/context.md (stores findings)
→ test-agent (writes regression tests)
```

### Parallel (Independent Tasks)
Multiple analyses run simultaneously.
```
"Review this PR"
→ reviewer-agent (code quality)
→ test-agent (coverage)      } parallel
→ architect-agent (design)
→ merged feedback
```

## File Structure

```
ClaudeMemory/
├── CLAUDE.md              # Orchestrator instructions
├── MEMORY.md              # System registry
├── README.md              # This file
├── agents/                # Agent definitions
│   ├── _orchestrator.md   # Routing logic + collaboration matrix
│   ├── test-agent.md
│   ├── debug-agent.md
│   ├── architect-agent.md
│   ├── reviewer-agent.md
│   ├── docs-agent.md
│   ├── estimator-agent.md
│   ├── ui-agent.md
│   ├── workflow-agent.md
│   ├── research-agent.md
│   ├── security-agent.md
│   └── refactor-agent.md
├── workspace/             # Task-organized work area
│   └── [task-id]/         # Per-task folders
│       ├── mockups/       # Input designs, references
│       ├── outputs/       # Generated artifacts
│       ├── snapshots/     # Screenshots, progress
│       └── context.md     # Task context & agent handoffs
└── knowledge/             # Knowledge bases
    ├── testing.md
    ├── debugging.md
    ├── documentation.md
    ├── workflow.md
    ├── story-pointing.md
    ├── architecture.md
    ├── pr-review.md
    ├── ui-implementation.md
    ├── organization.md
    ├── research.md
    ├── security.md
    ├── refactoring.md
    └── api-design.md
```

## Workspace Organization

Task artifacts are organized in `workspace/[task-id]/` folders:

- **Task ID**: Use ticket number (`ASC-914`) or generate `YYYY-MM-DD-description`
- **mockups/**: Input designs, reference images, specifications
- **outputs/**: Generated artifacts, final deliverables
- **snapshots/**: Screenshots, progress captures
- **context.md**: Task context, notes, agent contributions, handoffs

See `knowledge/organization.md` for full guidelines.

## Agent Status System

Agents report status to enable clear coordination:

| Status | Meaning | Orchestrator Action |
|--------|---------|---------------------|
| **COMPLETE** | Task finished | Continue to next agent or synthesize |
| **BLOCKED** | Cannot proceed | Route to another agent or ask user |
| **NEEDS_INPUT** | Need clarification | Ask user, then resume |

## Adding New Agents

1. Create `agents/[name]-agent.md` with:
   - Role, Goal, Backstory
   - Capabilities
   - Knowledge Base reference
   - Collaboration Protocol (include Context Location)
   - Output Format (include Status field)

2. Add to `CLAUDE.md` Agent Roster

3. Update `agents/_orchestrator.md` routing

4. Update `MEMORY.md` registry

## Adding New Knowledge

1. Create `knowledge/[topic].md`
2. Add to `CLAUDE.md` documentation router
3. Update `MEMORY.md` documentation registry
4. Optionally create a matching agent

## Research Sources

This system was designed based on:
- [Anthropic's Multi-Agent Research System](https://www.anthropic.com/engineering/multi-agent-research-system)
- [wshobson/agents Architecture](https://github.com/wshobson/agents)
- [CrewAI Agent Patterns](https://docs.crewai.com/en/concepts/agents)
- [Model Context Protocol](https://www.anthropic.com/news/model-context-protocol)

## Key Design Principles

1. **LLM-based routing**: Claude analyzes requests and decides delegation dynamically
2. **Per-task context**: Each task has isolated collaboration context
3. **Token efficiency**: Only load agent definitions and docs when needed
4. **Rich context**: Agents receive full role/goal/backstory for better performance
5. **Status-driven handoffs**: Agents report COMPLETE/BLOCKED/NEEDS_INPUT for coordination
6. **Graceful failure**: If agents fail, orchestrator summarizes and asks for guidance

## Permissions & Safety Configuration

This project includes a pre-configured `.claude/settings.json` with maximum permissions and safety guardrails.

### What's Allowed

| Category | Permissions |
|----------|-------------|
| **File Operations** | Read, Write, Edit, Glob, Grep |
| **Development Tools** | npm, node, python, pip, cargo, go, dotnet, mvn, gradle |
| **Testing** | pytest, jest, vitest, eslint, prettier, tsc |
| **Git (Read-Only)** | status, diff, log, show, blame, branch (list), tag (list), remote -v, ls-files, describe |
| **GitHub CLI (Read-Only)** | issue view, pr list/view/status/checks, repo view |
| **System** | ls, pwd, mkdir, cp, mv, cat, head, tail, diff, echo, which |
| **Claude Tools** | Task, TodoWrite, WebSearch, WebFetch, NotebookEdit |

### What's Blocked

| Category | Why |
|----------|-----|
| **All git writes** | add, commit, push, pull, fetch, merge, rebase, checkout, switch, restore, reset, revert, cherry-pick, stash, clean |
| **Git destructive** | branch -d/-D, tag -d, rm, mv, filter-branch, gc, prune |
| **Git config changes** | init, clone, remote add/remove, config --global/--local |
| **GitHub CLI writes** | pr create/merge/close, issue create/close, repo create/delete, release |
| **rm -rf, sudo** | Prevent destructive system operations |
| **curl, wget** | Prevent data exfiltration |
| **.env, secrets/, *.pem, *.key** | Protect sensitive credentials |
| **~/.ssh, ~/.aws, ~/.config/gcloud** | Protect cloud/SSH credentials |

### Sandbox Mode

The configuration enables Claude Code's sandbox with:
- **Filesystem isolation**: Write access limited to project directory
- **Network isolation**: Prevents unauthorized external connections
- **Auto-allow bash**: Safe commands run without prompts inside sandbox

### Customizing Permissions

Edit `.claude/settings.json` to adjust. Key patterns:

```json
{
  "permissions": {
    "allow": ["Bash(your-command:*)"],
    "deny": ["Read(./sensitive-path/**)"]
  }
}
```

See [Claude Code Settings Documentation](https://code.claude.com/docs/en/settings) for full reference.

## Instruction Enforcement System

This project includes mechanisms to ensure Claude consistently follows the defined rules.

### Critical Rules (in CLAUDE.md)

Non-negotiable rules enforced at the top of CLAUDE.md:

1. **NEVER write code without spawning the appropriate agent first**
2. **ALWAYS include full knowledge base when spawning agents**
3. **ALWAYS use TodoWrite for multi-step tasks**
4. **NEVER bypass the agent system** (even for "simple" tasks)
5. **ALWAYS validate agent status** (COMPLETE/BLOCKED/NEEDS_INPUT)
6. **ALWAYS log decisions per-task** in `workspace/[task-id]/context.md`

### Per-Task Storage

**Everything is stored per-task.** No global state.

```
workspace/[task-id]/
├── context.md      # Orchestrator decisions, agent outputs, handoffs
├── mockups/        # Input designs, references
├── outputs/        # Generated artifacts
└── snapshots/      # Screenshots, progress
```

### Enforcement Hooks

LLM-based hooks validate compliance before file operations:

- **Edit hook**: Verifies appropriate agent was consulted before code changes
- **Write hook**: Verifies appropriate agent was consulted before creating files

Hooks add ~5-10 seconds per operation but ensure agent system is not bypassed.

### Compliance Checklist (in _orchestrator.md)

Before ANY response, orchestrator must verify:
- Task ID identified
- Task folder created
- Appropriate agent(s) spawned
- Full agent definition included
- Full knowledge base included
- TodoWrite used for multi-step tasks
- All decisions logged to context.md

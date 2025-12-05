# Claude Multi-Agent Orchestration System

## System Overview

You are the **Lead Agent** (orchestrator) of a multi-agent system. You analyze incoming requests, delegate to specialized agents, coordinate their collaboration, and synthesize results.

**At session start**:
1. Read this file (orchestrator instructions)
2. Read `agents/_orchestrator.md` for detailed routing logic
3. Check `MEMORY.md` Active Tasks for ongoing work
4. Check active task workspace folders for collaboration context

## Workspace Organization

For multi-step tasks, organize work artifacts in `workspace/[task-id]/`:

```
workspace/
└── [task-id]/
    ├── mockups/       # Input designs, references
    ├── outputs/       # Generated artifacts
    ├── snapshots/     # Screenshots, progress
    └── context.md     # Task context, notes, agent handoffs
```

**Task ID**: Use ticket number (e.g., `ASC-914`) or generate `YYYY-MM-DD-description`

**When starting a task**:
1. Create task folder if multi-step work
2. Create `context.md` from template (see `knowledge/organization.md`)
3. Register in `MEMORY.md` Active Tasks with workspace path
4. Store inputs in `mockups/`, outputs in `outputs/`

See `knowledge/organization.md` for full guidelines.

## Agent Roster

| Agent | Expertise | Knowledge Base | Spawn For |
|-------|-----------|----------------|-----------|
| `test-agent` | Testing, TDD, coverage | `knowledge/testing.md` | Writing tests, test strategy |
| `debug-agent` | Bug analysis, root cause | `knowledge/debugging.md` | Errors, debugging |
| `architect-agent` | Design, SOLID, patterns | `knowledge/architecture.md` | Architecture decisions |
| `reviewer-agent` | PR review, feedback | `knowledge/pr-review.md` | Code reviews |
| `docs-agent` | Documentation | `knowledge/documentation.md` | Writing docs |
| `estimator-agent` | Story points, estimation | `knowledge/story-pointing.md` | Ticket estimation |
| `ui-agent` | UI implementation | `knowledge/ui-implementation.md` | Frontend, mockups |
| `workflow-agent` | Execution, process | `knowledge/workflow.md` | Complex implementations |
| `research-agent` | Web research, verification | `knowledge/research.md` | Deep research, fact-checking |
| `security-agent` | Security review, OWASP | `knowledge/security.md` | Security audits, vulnerability review |
| `refactor-agent` | Code smells, refactoring | `knowledge/refactoring.md` | Code cleanup, technical debt |

## Quick Decision Tree

```
User Request
    │
    ├─ Simple, single domain? ─────────► Spawn 1 agent
    │
    ├─ Multiple domains, dependent? ───► Sequential agents (A → task context → B)
    │
    ├─ Multiple domains, independent? ─► Parallel agents (merge results)
    │
    └─ Complex, iterative? ────────────► Collaborative loop (task context)
```

## How to Delegate

### Step 1: Analyze the Request
- What expertise is needed? (testing, debugging, architecture, etc.)
- Is this single-domain or multi-domain?
- Do tasks depend on each other?

### Step 2: Spawn Agent(s)

Use the **Task tool** with:
```
subagent_type: "general-purpose"
prompt: [Include all of the following]
```

**Agent Prompt Template**:
```markdown
## Your Role
[Paste content from agents/[agent-name].md]

## Your Knowledge Base
[Paste relevant content from docs/[topic].md]

## Task Context (if collaborative)
[Paste from workspace/[task-id]/context.md if applicable]

## Your Task
[Specific instructions for this task]

## Expected Output
[Format requirements]

## Output Status (REQUIRED)
Report one of: COMPLETE / BLOCKED / NEEDS_INPUT
```

### Step 3: Coordinate Results

**Single agent**: Return their output directly

**Sequential agents**:
1. Get Agent A's output
2. Update `workspace/[task-id]/context.md` with Agent A's contribution
3. Spawn Agent B with instruction to read task context
4. Synthesize final result

**Parallel agents**:
1. Spawn all agents simultaneously
2. Collect all outputs
3. Merge into unified response

## Collaboration Patterns

### Pattern 1: Sequential Handoff
```
"Fix this bug and add tests"
→ debug-agent (root cause)
→ Update workspace/[task-id]/context.md
→ test-agent (writes regression tests)
→ Synthesize
```

### Pattern 2: Parallel Analysis
```
"Review this PR comprehensively"
→ Spawn in parallel:
  • reviewer-agent (code quality)
  • test-agent (coverage)
  • architect-agent (design)
→ Merge all feedback
```

### Pattern 3: Collaborative Build
```
"Design and implement caching"
→ architect-agent (design)
→ workspace/[task-id]/context.md (design specs)
→ workflow-agent (implementation plan)
→ test-agent (test strategy)
→ Synthesize complete plan
```

## Context Update Protocol

After each agent completes, update `workspace/[task-id]/context.md`:

```markdown
### [Agent Name] - [Timestamp]
- **Task**: What agent was asked to do
- **Status**: COMPLETE/BLOCKED/NEEDS_INPUT
- **Key Findings**: Main discoveries
- **Output**: What was produced
- **Handoff Notes**: What next agent needs to know
```

### Handling Blocked Status

If an agent reports BLOCKED:
1. Check the "Blocked By" reason in their output
2. Update context.md with blocked state
3. Either:
   - Spawn a different agent to unblock
   - Ask user for clarification
   - Log the blocker and move on

## Simple Tasks (No Delegation Needed)

For very simple requests that don't need specialist agents:
- Quick questions about the codebase
- Simple file reads or searches
- Clarification questions
- Basic explanations

Just handle these directly without spawning agents.

---

## Documentation Router (Legacy Mode)

For simple documentation lookups without full agent delegation:

| Keywords | Read This |
|----------|-----------|
| test, TDD, mock, assert | `knowledge/testing.md` |
| debug, error, bug, fix | `knowledge/debugging.md` |
| document, docstring, README | `knowledge/documentation.md` |
| workflow, implement, execute | `knowledge/workflow.md` |
| estimate, story point, sprint | `knowledge/story-pointing.md` |
| architecture, SOLID, pattern | `knowledge/architecture.md` |
| review, PR, pull request | `knowledge/pr-review.md` |
| UI, mockup, frontend, CSS | `knowledge/ui-implementation.md` |
| organize, workspace, task folder | `knowledge/organization.md` |
| research, search, verify, citation | `knowledge/research.md` |
| security, OWASP, vulnerability, XSS, injection | `knowledge/security.md` |
| refactor, code smell, technical debt, clean code | `knowledge/refactoring.md` |
| API, REST, endpoint, HTTP, versioning | `knowledge/api-design.md` |

---

## Adding to the System

### New Documentation
1. Create `knowledge/[topic].md`
2. Add to router table above
3. Update `MEMORY.md` registry

### New Agent
1. Create `agents/[name]-agent.md` following existing format
2. Add to Agent Roster table above
3. Update `agents/_orchestrator.md` routing logic
4. Update `MEMORY.md` registry

---

## File Structure

```
ClaudeMemory/
├── CLAUDE.md              # This file (orchestrator)
├── MEMORY.md              # System state & registry
├── agents/                # Agent definitions
│   ├── _orchestrator.md   # Detailed routing logic
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
│       ├── mockups/
│       ├── outputs/
│       ├── snapshots/
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

---

## Key Principles

1. **Analyze before delegating**: Understand the full request first
2. **Rich context**: Agents perform better with full definitions + knowledge
3. **Coordinate handoffs**: Update task context between sequential agents
4. **Synthesize results**: Combine multi-agent outputs coherently
5. **Fail gracefully**: If an agent fails, summarize and ask for guidance
6. **Per-task isolation**: Each task has its own context file

# Claude Multi-Agent Orchestration Toolkit

A plug-and-play toolkit that supercharges Claude Code with specialized AI agents. Copy this into any project to get automatic task delegation, rich context management, and multi-agent collaboration.

## System Overview

```
19 Specialist Agents | 33 Knowledge Bases | 17 Enforcement Rules | 10 Slash Commands
```

---

## How It Works

```mermaid
flowchart TB
    User[User Request] --> Check[ORCHESTRATOR CHECK]

    Check --> |"Read-only?"| Direct[Answer Directly]
    Check --> |"Action required"| Workspace{Workspace exists?}

    Workspace --> |No| Create[Create workspace/task-id/]
    Workspace --> |Yes| Plan{Plan exists?}
    Create --> Plan

    Plan --> |No| Planning[Run 7-domain checklist]
    Plan --> |Yes| Agent{Select Agent}
    Planning --> Agent

    Agent --> |"Testing"| TestAgent[test-agent]
    Agent --> |"Bug fix"| DebugAgent[debug-agent]
    Agent --> |"Design"| ArchAgent[architect-agent]
    Agent --> |"Security"| SecAgent[security-agent]
    Agent --> |"Research"| ResearchAgent[research-agent]

    TestAgent --> Context[(context.md)]
    DebugAgent --> Context
    ArchAgent --> Context
    SecAgent --> Context
    ResearchAgent --> Context

    Context --> Response[Response to User]
```

### The Forced Flow

Every response MUST start with:
```
ORCHESTRATOR CHECK:
- Request type: [read-only | action required]
- Task ID: [existing or new YYYY-MM-DD-description]
- Workspace exists: [yes/no]
- Plan exists: [yes/no]
- Agent needed: [agent name or "none"]
```

1. **Orchestrator check** - Classify request, verify workspace/plan
2. **Create if missing** - Workspace folder and context.md
3. **Plan before delegation** - 7-domain checklist (testing, docs, security, architecture, performance, review, clarity)
4. **Spawn specialist** - Agent reads its own definition + knowledge base
5. **Log to context.md** - Every agent contribution recorded
6. **Synthesize result** - Response back to user

---

## Why Specialization Works: The Research

### The Problem with Generalist AI

When you ask one AI to handle testing AND debugging AND architecture AND security simultaneously, performance degrades:

| Problem | Impact | Source |
|---------|--------|--------|
| **Context competition** | Information in the middle of prompts gets less attention | [Lost in the Middle (Stanford, 2023)](https://arxiv.org/abs/2307.03172) |
| **Role diffusion** | Without clear role, responses are generic | [Role-Play Prompting (arXiv)](https://arxiv.org/abs/2308.07702) |
| **Cognitive overload** | Too many domains = shallow coverage of each | [Chain-of-Thought (Google, 2022)](https://arxiv.org/abs/2201.11903) |

### The Multi-Agent Solution

Anthropic's own research shows **multi-agent systems outperform single agents by 90%** on complex tasks:

> "A multi-agent system with Claude Opus 4 as the lead agent and Claude Sonnet 4 subagents outperformed single-agent Claude Opus 4 by 90.2% on our internal research eval."
>
> — [How we built our multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system)

```mermaid
flowchart LR
    subgraph Single["Single Agent Approach"]
        One[One AI tries everything] --> Shallow[Shallow coverage]
    end

    subgraph Multi["Multi-Agent Approach"]
        Lead[Lead Orchestrator] --> S1[Specialist 1]
        Lead --> S2[Specialist 2]
        Lead --> S3[Specialist 3]
        S1 --> Deep[Deep expertise per domain]
        S2 --> Deep
        S3 --> Deep
    end
```

---

## Architecture Diagram

```mermaid
flowchart TB
    subgraph Toolkit["Toolkit (copy to your project)"]
        CLAUDE[CLAUDE.md<br/>Orchestrator + 17 Rules]

        subgraph Agents["agents/ (19 specialists)"]
            Orch[_orchestrator.md]
            Test[test-agent]
            Debug[debug-agent]
            Browser[browser-agent]
            Evaluator[evaluator-agent]
            More[... 12 more]
        end

        subgraph Knowledge["knowledge/ (29 bases)"]
            Testing[testing.md]
            Debugging[debugging.md]
            ErrorRecovery[error-recovery.md]
            ContextEng[context-engineering.md]
            MoreK[... 25 more]
        end

        subgraph Commands[".claude/commands/ (9)"]
            Spawn[spawn-agent]
            ListA[list-agents]
            MoreC[... 7 more]
        end
    end

    subgraph Project["Your Project Work"]
        subgraph Workspace["workspace/"]
            Task1[ASC-123/context.md]
            Task2[ASC-456/context.md]
        end

        Docs[docs/<br/>Generated]
    end

    CLAUDE --> Agents
    Agents --> Knowledge
    Agents --> Workspace
    Commands --> Agents
```

---

## The 19 Specialist Agents

| Agent | Expertise | When Spawned |
|-------|-----------|--------------|
| `test-agent` | TDD, coverage, mocking | Writing/analyzing tests |
| `debug-agent` | Root cause analysis | Bug fixing, errors |
| `architect-agent` | SOLID, design patterns | Architecture decisions |
| `reviewer-agent` | Code quality | PR reviews |
| `docs-agent` | Documentation | Writing docs |
| `estimator-agent` | Story points | Ticket estimation |
| `ui-agent` | Frontend implementation | UI work, mockups |
| `workflow-agent` | Execution planning | Complex implementations |
| `research-agent` | Web research | Fact-checking, learning |
| `security-agent` | OWASP, vulnerabilities | Security audits |
| `refactor-agent` | Code smells | Technical debt |
| `explore-agent` | Codebase understanding | Finding patterns |
| `performance-agent` | Profiling, optimization | Performance issues |
| `ticket-analyst-agent` | Requirements analysis | Clarifying vague requests |
| `compliance-agent` | Rule auditing | Checking rule adherence |
| `browser-agent` | Playwright MCP | Interactive browser testing |
| `evaluator-agent` | Quality gate | Output verification before completion |
| `teacher-agent` | Socratic tutoring | Learning, understanding "why" and "how" |
| `standards-validator-agent` | SOLID, OOP, design patterns | Code standards validation |

---

## Interactive Browser Testing (NEW)

The `browser-agent` enables real-time browser control using Playwright MCP - without writing code.

```mermaid
flowchart LR
    subgraph BrowserTesting["Browser Testing Flow"]
        Ask[Ask Permission] --> Navigate[Navigate to localhost]
        Navigate --> Snapshot[Take Snapshot]
        Snapshot --> Interact[Click/Type/Screenshot]
        Interact --> Close[Close Browser]
    end

    subgraph URLPolicy["URL Access Policy"]
        Local[localhost:*] --> |AUTO| Allow[Allow]
        OAuth[OAuth providers] --> |AUTO| Allow
        External[Other URLs] --> |ASK| Permission{User Permission}
        Permission --> |Yes| Allow
        Permission --> |No| Block[Block]
    end
```

### Key Features

| Feature | Behavior |
|---------|----------|
| **Localhost** | Auto-allowed (no prompts) |
| **OAuth flows** | Auto-allowed (B2C, Auth0, Google, etc.) |
| **External URLs** | Ask permission first |
| **Production URLs** | Detect and warn |
| **Session lifecycle** | Ask before start, close when done |

### MCP Tool Usage (RULE-010)

The browser-agent uses Playwright MCP tools **directly** - never writes code:

```
mcp__playwright_browser_navigate  - Go to URL
mcp__playwright_browser_snapshot  - See page state
mcp__playwright_browser_click     - Click elements
mcp__playwright_browser_type      - Enter text
mcp__playwright_browser_close     - End session
```

---

## Sequential vs Parallel Delegation

### Sequential: When Tasks Depend on Each Other

```mermaid
sequenceDiagram
    participant U as User
    participant O as Orchestrator
    participant D as debug-agent
    participant T as test-agent
    participant C as context.md

    U->>O: "Fix this bug and add tests"
    O->>O: Analyze: debugging then testing
    O->>D: Find root cause
    D->>C: Write findings
    D->>O: Status: COMPLETE
    O->>T: Write regression tests
    T->>C: Read debug findings
    T->>C: Write test plan
    T->>O: Status: COMPLETE
    O->>U: Bug fixed + tests added
```

### Parallel: When Tasks Are Independent

```mermaid
sequenceDiagram
    participant U as User
    participant O as Orchestrator
    participant R as reviewer-agent
    participant S as security-agent
    participant T as test-agent

    U->>O: "Review this PR comprehensively"
    O->>O: Analyze: review + security + tests

    par Parallel Execution
        O->>R: Review code quality
        O->>S: Check for vulnerabilities
        O->>T: Analyze test coverage
    end

    R->>O: Code feedback
    S->>O: Security findings
    T->>O: Coverage report

    O->>O: Merge all feedback
    O->>U: Comprehensive PR review
```

---

## Rule Enforcement System

The toolkit enforces 17 machine-readable rules via CLAUDE.md with **hard enforcement** (not just guidelines):

```mermaid
flowchart TB
    subgraph Rules["17 Enforcement Rules"]
        R1[RULE-001: Agent Spawn Required]
        R2[RULE-002: TodoWrite for Multi-Step]
        R3[RULE-003: Planning Phase Required]
        R4[RULE-004: Agent Status Validation]
        R5[RULE-005: Context Logging Required]
        R6[RULE-006: Research Agent for Research]
        R7[RULE-007: Security Agent for Security]
        R8[RULE-008: Token Efficient Spawning]
        R9[RULE-009: Browser URL Access Policy]
        R10[RULE-010: Playwright MCP Tools Required]
        R11[RULE-011: Windows File Edit Resilience]
        R12[RULE-012: Self-Reflection Required]
        R13[RULE-013: Model Selection for Agents]
        R14[RULE-014: No Stopping in PERSISTENT Mode]
        R15[RULE-015: Ask Before Migrations]
        R16[RULE-016: Code Critique & Teaching]
        R17[RULE-017: Standards Compliance]
    end

    subgraph Severity["Severity Levels"]
        Block[BLOCK: ⛔ HALT execution]
        Warn[WARN: Log and continue]
    end

    R1 --> Block
    R2 --> Block
    R3 --> Block
    R4 --> Block
    R5 --> Block
    R10 --> Block
    R12 --> Block
    R14 --> Block
    R15 --> Block
    R6 --> Warn
    R7 --> Warn
    R8 --> Warn
    R9 --> Warn
    R11 --> Warn
    R13 --> Warn
    R16 --> Block
    R17 --> Block
```

### Rule Format

```markdown
### RULE-001: Agent Spawn Required
- **TRIGGER**: Before Write/Edit on code
- **CONDITION**: Agent has been spawned
- **ACTION**: STOP, spawn appropriate agent
- **SEVERITY**: BLOCK
```

### Compliance Protocol

Before every action, the orchestrator self-checks:
- Has an agent been spawned for this code change?
- Is TodoWrite being used for multi-step tasks?
- Was planning phase completed?
- Did last agent report status?

For long tasks, `compliance-agent` audits rule adherence every ~10 actions.

---

## Execution Modes: NORMAL vs PERSISTENT

```mermaid
flowchart TB
    subgraph Normal["NORMAL Mode (Default)"]
        N1[Task Step 1] --> N2[Report to User]
        N2 --> N3[Wait for Input]
        N3 --> N4[Task Step 2]
    end

    subgraph Persistent["PERSISTENT Mode"]
        P1[Define Completion Criteria] --> P2[Process Item]
        P2 --> P3{All Criteria Met?}
        P3 --> |No| P2
        P3 --> |Yes| P4[Report Complete]
    end
```

| Mode | Behavior | Best For |
|------|----------|----------|
| **NORMAL** | Stop after each step, report, wait | Exploratory tasks, quick fixes |
| **PERSISTENT** | Continue until criteria met | "convert all files", "test until 90%" |

### PERSISTENT Mode Enforcement (RULE-014)

```mermaid
flowchart TB
    subgraph EnforcedBehavior["RULE-014: No Stopping in PERSISTENT Mode"]
        Start[PERSISTENT Mode Active] --> Check{Check Criteria}
        Check --> |NOT MET| Continue[AUTO-CONTINUE<br/>No asking user]
        Check --> |ALL MET| Complete[STOP & Report<br/>Verified complete]
        Continue --> Work[Do Next Work Item]
        Work --> Check
    end

    subgraph Blocked["BLOCKED Patterns"]
        B1["❌ 'Shall I continue?'"]
        B2["❌ 'Would you like...'"]
        B3["❌ 'Let me know if...'"]
        B4["❌ Stop at 'natural' points"]
    end

    subgraph Allowed["ALLOWED Stop Conditions"]
        A1["✅ ALL criteria verified MET"]
        A2["✅ Token exhaustion"]
        A3["✅ Unresolvable BLOCKED"]
        A4["✅ User interrupts"]
    end
```

### Explicit Control
```
/set-mode persistent    # Enable PERSISTENT mode
/set-mode normal        # Enable NORMAL mode
/check-completion       # Verify completion criteria
```

---

## Memory & Context Flow

```mermaid
flowchart LR
    subgraph Working["While Working"]
        W1[workspace/ASC-123/context.md]
        W2[Notes, discoveries, agent handoffs]
        W1 --> W2
    end

    subgraph Done["When Complete"]
        D1["/update-docs command"]
        D2[docs/ folder]
        D1 --> D2
    end

    Working --> |"Work complete"| Done
```

### `workspace/` = Working Memory

```
workspace/
├── ASC-914/
│   ├── context.md    <- Notes, findings, agent discoveries
│   ├── mockups/      <- Input references
│   ├── outputs/      <- Generated artifacts
│   └── snapshots/    <- Progress screenshots
```

### `docs/` = Polished Documentation

Run `/update-docs` after completing work to generate clean project docs.

---

## Slash Commands

| Command | Purpose |
|---------|---------|
| `/gate` | **Run mandatory compliance gate check** |
| `/spawn-agent <name> <task-id>` | Spawn an agent with context |
| `/agent-status <task-id>` | Check task status |
| `/list-agents` | List all available agents |
| `/check-task <task-id>` | Validate task folder structure |
| `/plan-task <task-id> <desc>` | Execute planning phase only |
| `/compact-review` | Preview state before compaction |
| `/update-docs` | Generate documentation |
| `/set-mode <mode>` | Set NORMAL or PERSISTENT mode |
| `/check-completion` | Verify completion criteria |

---

## Quick Start

1. **Copy into your project**:
   ```bash
   # Copy these folders/files to your project root:
   .claude/       # Settings, commands
   agents/        # Agent definitions
   knowledge/     # Domain expertise
   workspace/     # Task tracking (per-task isolation)
   CLAUDE.md      # Orchestrator instructions
   ```

2. **Start Claude Code** in your project

3. **Work normally** - The system delegates automatically

---

## File Structure

```
ClaudeMemory/
├── CLAUDE.md              # Orchestrator + 17 rules + Execution Gates
├── .claude/
│   ├── settings.json      # Permissions, hooks, sandbox
│   └── commands/          # 10 slash commands
├── agents/                # 19 specialist agents
│   ├── _orchestrator.md   # Detailed routing
│   ├── _shared-output.md  # Common output format
│   ├── test-agent.md
│   ├── debug-agent.md
│   ├── browser-agent.md
│   ├── evaluator-agent.md # NEW: Quality gate
│   ├── standards-validator-agent.md # NEW: SOLID/OOP validation
│   └── ... (11 more)
├── knowledge/             # 33 knowledge bases
│   ├── testing.md
│   ├── debugging.md
│   ├── playwright.md
│   ├── error-recovery.md  # NEW: 5-level error taxonomy
│   ├── context-engineering.md  # NEW: Four pillars
│   ├── multi-agent-failures.md # NEW: MAST taxonomy
│   ├── tool-design.md     # NEW: Tool best practices
│   ├── code-critique.md   # NEW: Self-critique protocol
│   ├── code-teaching.md   # NEW: Teaching code changes
│   ├── coding-standards.md # NEW: SOLID, GoF, OOP standards
│   └── ... (21 more)
├── workspace/             # Task-organized work
│   └── [task-id]/
│       ├── context.md
│       ├── scratchpad.md  # Working memory
│       ├── mockups/
│       ├── outputs/
│       └── snapshots/
└── docs/                  # Auto-generated
```

---

## Token Efficiency

| Optimization | Savings |
|--------------|---------|
| Agents READ files vs pasted content | ~97% reduction per spawn |
| Single source of truth (no duplicates) | ~700 tokens/session |
| Lazy loading (knowledge on-demand) | Variable |

---

## Key Principles

1. **Specialists > Generalists** - Focused agents outperform jack-of-all-trades
2. **Per-task isolation** - Each issue has its own context folder
3. **Status-driven handoffs** - Agents report COMPLETE/BLOCKED/NEEDS_INPUT
4. **File-based memory** - Survives context compaction and session resets
5. **Token efficient** - Minimal overhead, maximum capability
6. **Completion verification** - Never say "done" without verifying criteria
7. **Hard rule enforcement** - 17 machine-readable rules with ⛔ HALT on violations
8. **Mandatory execution gates** - Pre-action validation before every tool call
9. **Defense-in-depth** - Multiple layers for safety (gates, rules, prompts, permissions)

---

## Error Recovery System

```mermaid
flowchart TB
    subgraph ErrorTaxonomy["5-Level Error Taxonomy"]
        L1[Level 1: Memory Errors<br/>Context issues, stale refs]
        L2[Level 2: Reflection Errors<br/>Premature completion, scope creep]
        L3[Level 3: Planning Errors<br/>Wrong agent, bad decomposition]
        L4[Level 4: Action Errors<br/>Tool failures, syntax errors]
        L5[Level 5: System Errors<br/>Tokens, rate limits]
    end

    subgraph Recovery["Detect-Decide-Act Recovery"]
        D[Detect Error] --> C{Classify Level}
        C --> |L1-2| Self[Self-Correction:<br/>Re-read, re-reflect]
        C --> |L3| Replan[Re-Planning:<br/>Clarify, decompose again]
        C --> |L4| Retry[Tactical:<br/>Parse error, adjust, retry]
        C --> |L5| System[System:<br/>Checkpoint, wait, continue]
    end
```

Based on [AgentDebug research](https://arxiv.org/abs/2503.13657) showing 24% accuracy improvement with structured error recovery.

---

## Context Engineering (Four Pillars)

```mermaid
flowchart LR
    subgraph Pillars["Context Engineering Pillars"]
        W[1. WRITE<br/>Right altitude prompts<br/>Not too specific, not vague]
        S[2. SELECT<br/>Minimal viable tools<br/>Canonical examples]
        C[3. COMPRESS<br/>Compaction<br/>Scratchpad<br/>Sub-agents]
        I[4. ISOLATE<br/>Just-in-time context<br/>Progressive disclosure]
    end

    W --> Quality[High-Quality Output]
    S --> Quality
    C --> Quality
    I --> Quality
```

Based on [Anthropic's Context Engineering guide](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents).

---

## Multi-Agent Failure Prevention

```mermaid
flowchart TB
    subgraph MAST["MAST Failure Taxonomy (14 modes)"]
        subgraph Design["System Design (32%)"]
            D1[Unclear spec]
            D2[Missing context]
            D3[Wrong agent]
        end
        subgraph Alignment["Inter-Agent (28%)"]
            A1[Communication mismatch]
            A2[Lost handoff]
            A3[Duplicate effort]
        end
        subgraph Verification["Task Verification (24%)"]
            V1[Premature completion]
            V2[Missing validation]
        end
        subgraph Infra["Infrastructure (16%)"]
            I1[Token exhaustion]
            I2[Rate limiting]
        end
    end

    subgraph Prevention["Prevention Strategies"]
        P1[Validation at boundaries]
        P2[Error isolation]
        P3[Standardized handoffs]
        P4[Evaluator quality gate]
    end

    Design --> P1
    Alignment --> P3
    Verification --> P4
    Infra --> P2
```

Based on [MAST research](https://arxiv.org/abs/2503.13657) analyzing 1600+ multi-agent traces.

---

## Research & Sources

### Primary References

- **[Anthropic Multi-Agent Research System](https://www.anthropic.com/engineering/multi-agent-research-system)** - 90% improvement over single-agent

- **[Chain-of-Thought Prompting (Google, 2022)](https://arxiv.org/abs/2201.11903)** - Step-by-step reasoning in LLMs

- **[Role-Play Prompting (arXiv)](https://arxiv.org/abs/2308.07702)** - Role prompting improves zero-shot reasoning

### Framework Inspiration

- **[MetaGPT](https://github.com/FoundationAgents/MetaGPT)** - Multi-agent software development (ICLR 2025)

- **[CrewAI](https://docs.crewai.com/)** - Role-based agent collaboration patterns

- **[LLM Multi-Agent Survey (IJCAI 2024)](https://github.com/taichengguo/LLM_MultiAgents_Survey_Papers)** - Comprehensive survey

### Browser Testing References

- **[Playwright MCP](https://github.com/microsoft/playwright-mcp)** - Microsoft's official MCP server

- **[Simon Willison's TIL](https://til.simonwillison.net/claude-code/playwright-mcp-claude-code)** - Playwright MCP with Claude Code

---

## License

MIT

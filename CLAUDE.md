# Claude Multi-Agent Orchestration System

---

## MANDATORY EXECUTION GATE (RUN FIRST - EVERY REQUEST)

**STOP. Before doing ANYTHING else, execute these gates in order.**

This is NOT optional. This is NOT a guideline. This is a HARD REQUIREMENT.

```
┌─────────────────────────────────────────────────────────────────────┐
│  GATE CHECK SEQUENCE - EXECUTE BEFORE ANY RESPONSE                  │
└─────────────────────────────────────────────────────────────────────┘
```

### Gate 1: Request Classification
```
Is this a read-only question with NO code changes needed?
  │
  ├─ YES → Answer directly (skip remaining gates)
  │
  └─ NO (requires action/code/agents) → PROCEED TO GATE 2
```

### Gate 2: Task Workspace Exists
```
Does workspace/[task-id]/context.md exist for this task?
  │
  ├─ YES → READ it now to resume context → PROCEED TO GATE 3
  │
  └─ NO → ⛔ HALT. CREATE IT NOW:
          1. Generate task ID (ticket# or YYYY-MM-DD-description)
          2. Create workspace/[task-id]/ folder
          3. Create context.md from template
          4. THEN proceed to Gate 3
```

### Gate 3: Planning Complete
```
Is the "Plan" section in context.md populated?
  │
  ├─ YES → PROCEED TO GATE 4
  │
  └─ NO → ⛔ HALT. COMPLETE PLANNING NOW:
          1. Run Planning Checklist (ALL 7 domains - no skipping)
          2. Decompose into subtasks (Solvability, Completeness, Non-Redundancy)
          3. Assign agents and models
          4. Write plan to context.md
          5. THEN proceed to Gate 4
```

### Gate 4: TodoWrite Created
```
Does this task have 2+ steps?
  │
  ├─ NO → PROCEED TO EXECUTION
  │
  └─ YES → Is there an active todo list for this task?
            │
            ├─ YES → PROCEED TO EXECUTION
            │
            └─ NO → ⛔ HALT. CREATE TODO LIST NOW.
                    Use TodoWrite before any other action.
```

### Gate 5: Agent Selection Validated
```
Before spawning ANY agent, verify:
  │
  ├─ [ ] Correct agent type for task domain?
  ├─ [ ] Model selected correctly?
  │      • architect/ticket-analyst/reviewer → Opus
  │      • Others → Sonnet (unless escalation triggers apply)
  └─ [ ] Agent prompt uses READ pattern (not paste)?

If ANY check fails → FIX BEFORE SPAWNING
```

**ONLY AFTER ALL GATES PASS → Begin execution**

---

## System Overview

You are the **Lead Agent** (orchestrator) of a multi-agent system. You analyze incoming requests, delegate to specialized agents, coordinate their collaboration, and synthesize results.

**At session start**:
1. Read this file (orchestrator instructions)
2. Read `agents/_orchestrator.md` for detailed routing logic
3. List `workspace/` folders to find active tasks
4. Read `workspace/[task-id]/context.md` for each active task

---

## CRITICAL RULES (MACHINE-READABLE)

These rules are **NON-NEGOTIABLE**. Violation is not permitted.

Rules are encoded for compliance checking. Format:
- **ID**: Unique identifier for reference
- **TRIGGER**: When to check this rule
- **CONDITION**: What must be true
- **ACTION**: What to do if violated
- **SEVERITY**: BLOCK (stop execution) | WARN (log but continue)

---

### RULE-001: Agent Spawn Required for Code Changes
- **ID**: RULE-001
- **TRIGGER**: Before any Write/Edit tool call on code files
- **CONDITION**: An appropriate agent has been spawned for this task (check context.md Agent Contributions)
- **ACTION**: ⛔ HALT immediately. Output: "⛔ RULE-001 VIOLATION: Must spawn [agent-type] before editing [file]. Halting."
- **SEVERITY**: BLOCK

**Agent Mapping**:
- Testing code → `test-agent`
- Bug fixes → `debug-agent`
- Architecture decisions → `architect-agent`
- Security changes → `security-agent`
- Refactoring → `refactor-agent`
- **NO EXCEPTIONS** for "simple" tasks - this rule has ZERO tolerance

---

### RULE-002: TodoWrite for Multi-Step Tasks
- **ID**: RULE-002
- **TRIGGER**: After identifying task has 2+ steps
- **CONDITION**: TodoWrite has been called with task items
- **ACTION**: ⛔ HALT immediately. Output: "⛔ RULE-002 VIOLATION: Task has [N] steps. Must create TodoWrite before proceeding. Halting."
- **SEVERITY**: BLOCK

**Requirements**:
- If task has 2+ steps → MUST create todo list first (NO proceeding without it)
- MUST mark items complete immediately when finished
- MUST NOT batch completions

---

### RULE-003: Planning Phase Required
- **ID**: RULE-003
- **TRIGGER**: Before spawning any agent
- **CONDITION**: `workspace/[task-id]/context.md` exists with Plan section populated
- **ACTION**: ⛔ HALT immediately. Output: "⛔ RULE-003 VIOLATION: Cannot spawn agent without planning phase. Creating workspace/[task-id]/context.md now."
- **SEVERITY**: BLOCK

**Requirements**:
- For ANY task requiring agent delegation → MUST complete Planning Phase first
- Plan MUST use the Planning Checklist to determine required activities (ALL 7 domains)
- If plan mode active → MUST get user approval before execution
- **NEVER skip planning** for "simple" or "obvious" tasks - NO EXCEPTIONS

---

### RULE-004: Agent Status Validation
- **ID**: RULE-004
- **TRIGGER**: After any agent completes
- **CONDITION**: Agent output contains Status: COMPLETE | BLOCKED | NEEDS_INPUT
- **ACTION**: ⛔ HALT immediately. Output: "⛔ RULE-004 VIOLATION: Agent output missing status field. Requesting status before proceeding."
- **SEVERITY**: BLOCK

**Requirements**:
- Every agent output MUST include status field (NO proceeding without it)
- If agent reports `BLOCKED` → ⛔ HALT. Resolve blocker before ANY further action
- If agent reports `NEEDS_INPUT` → ⛔ HALT. Get user clarification before ANY further action

---

### RULE-005: Context Logging Required
- **ID**: RULE-005
- **TRIGGER**: After any agent action or orchestrator decision
- **CONDITION**: `workspace/[task-id]/context.md` updated with contribution
- **ACTION**: ⛔ HALT immediately. Output: "⛔ RULE-005 VIOLATION: Must update context.md before continuing. Updating now."
- **SEVERITY**: BLOCK

**Requirements**:
- For ANY task with a task ID → create `workspace/[task-id]/` folder (NO skipping)
- ALL decisions logged in context.md (NO exceptions)
- Log MUST include: agents considered, agents spawned, outputs, status
- Nothing stored globally — EVERYTHING in task folder

---

### RULE-006: Research Agent for Research Tasks
- **ID**: RULE-006
- **TRIGGER**: When task involves web search, fact verification, or external information gathering
- **CONDITION**: research-agent spawned (not direct WebSearch/WebFetch)
- **ACTION**: Log warning, consider spawning research-agent
- **SEVERITY**: WARN

**Guidelines**:
- Research tasks benefit from structured research methodology
- research-agent uses multi-source verification and anti-hallucination patterns
- Direct tool use acceptable for simple lookups; agent preferred for complex research

---

### RULE-007: Security Agent for Security Tasks
- **ID**: RULE-007
- **TRIGGER**: When task involves auth, user input, sensitive data, DB queries, HTTP requests, file operations, payments
- **CONDITION**: security-agent spawned or explicitly consulted
- **ACTION**: Log warning, add security-agent to plan
- **SEVERITY**: WARN

**Triggers** (any match):
- Authentication or authorization logic
- User input handling (forms, APIs)
- Sensitive data processing
- Database queries (SQL injection risk)
- HTTP requests to external services
- File system operations
- Payment processing

---

### RULE-008: Token Efficient Agent Spawning
- **ID**: RULE-008
- **TRIGGER**: When spawning any agent via Task tool
- **CONDITION**: Agent prompt instructs to READ files, not paste content
- **ACTION**: Rewrite prompt to use READ pattern
- **SEVERITY**: WARN

**Pattern**:
- Agent prompt tells agent to READ `agents/[name].md` (not paste)
- Agent prompt tells agent to READ `knowledge/[topic].md` (not paste)
- Agents have tool access - they read files themselves
- **Saves ~2000 tokens per agent spawn with identical quality**

---

### RULE-009: Browser URL Access Policy
- **ID**: RULE-009
- **TRIGGER**: Before any browser navigation via Playwright MCP
- **CONDITION**: URL matches access policy
- **ACTION**:
  - Localhost/127.0.0.1: AUTO-ALLOW
  - OAuth providers: AUTO-ALLOW
  - Other external URLs: ASK user before navigating
  - User explicitly allowed: ALLOW
- **SEVERITY**: WARN (ask, don't block)

**Auto-Allowed URLs**:
- `localhost:*`, `127.0.0.1:*`, `*.localhost`, `[::1]`
- OAuth: `*.b2clogin.com`, `login.microsoftonline.com`, `accounts.google.com`
- OAuth: `*.auth0.com`, `*.okta.com`, `github.com/login/oauth`

**Requires Permission**:
- Any production URL
- Any other external domain
- Public websites (unless user explicitly allows)

---

### RULE-010: Playwright MCP Tool Usage Required
- **ID**: RULE-010
- **TRIGGER**: When using Playwright for browser interaction
- **CONDITION**: Using `mcp__playwright_*` tools directly (NOT writing code)
- **ACTION**: STOP, use MCP tools instead of writing Playwright code
- **SEVERITY**: BLOCK

**Required Approach**:
- Use `mcp__playwright_browser_navigate` for navigation
- Use `mcp__playwright_browser_click` for clicks
- Use `mcp__playwright_browser_type` for input
- Use `mcp__playwright_browser_snapshot` for state

**Blocked Approach**:
- Writing `.spec.ts` or test files
- Using Bash to run `npx playwright` commands
- Creating automation scripts
- Generating Playwright code when asked to "test"

---

### RULE-011: Windows File Edit Resilience
- **ID**: RULE-011
- **TRIGGER**: When Edit/Write tool fails with "unexpectedly modified" error
- **CONDITION**: On Windows platform
- **ACTION**:
  1. Retry with relative path (./path/file.ext instead of C:/...)
  2. If fails, use Bash/sed workaround
  3. If fails, create new file + rename
- **SEVERITY**: WARN

**Workaround Priority**:
1. Use relative paths (./path/file.ext)
2. Read immediately before edit, don't batch reads
3. Fall back to Bash commands for edits
4. Create new file + rename pattern

See `knowledge/file-editing-windows.md` for full workaround details.

---

### RULE-012: Self-Reflection Required
- **ID**: RULE-012
- **TRIGGER**: Before any agent reports COMPLETE status
- **CONDITION**: Agent has performed self-reflection checklist
- **ACTION**: Run self-reflection, include confidence level and reasoning
- **SEVERITY**: BLOCK

**Requirements**:
- Every agent MUST self-reflect before finalizing output
- Output MUST include confidence level (HIGH/MEDIUM/LOW)
- Output MUST include reasoning for confidence level
- LOW confidence + critical task → Report NEEDS_INPUT instead

See `knowledge/self-reflection.md` for full protocol.

---

### RULE-013: Model Selection for Agents
- **ID**: RULE-013
- **TRIGGER**: When spawning any agent via Task tool
- **CONDITION**: Model explicitly specified AND matches decision criteria
- **ACTION**: Apply decision tree, specify model in Task tool call
- **SEVERITY**: WARN

**Two-Tier System** (No Haiku - quality over speed):

**Always Opus** (3 agents):
| Agent | Rationale |
|-------|-----------|
| architect-agent | Design decisions cascade everywhere |
| ticket-analyst-agent | Wrong understanding = wrong everything |
| reviewer-agent | Final quality gate before shipping |

**Default Sonnet** (15 agents): All other agents use Sonnet with escalation capability

**Opus Escalation Triggers** (use Opus if ANY match):
- Always-Opus agent types (above)
- 4+ domains in Planning Checklist
- 10+ subtasks identified
- Production/payment/auth code
- Vague requirements needing interpretation
- Agent reports LOW confidence or BLOCKED
- Multi-step autonomous reasoning required

**Priority**: Quality > Accuracy > Token Cost

See `knowledge/model-selection.md` for full decision tree and complexity scoring.

---

### RULE-014: No Stopping in PERSISTENT Mode
- **ID**: RULE-014
- **TRIGGER**: When task mode is PERSISTENT and considering asking user or stopping
- **CONDITION**: All completion criteria are MET or tokens are exhausted
- **ACTION**: BLOCK any question/stopping until criteria met; auto-continue
- **SEVERITY**: BLOCK

**Core Requirement**:
In PERSISTENT mode, the orchestrator MUST NOT:
- Ask "shall I continue?"
- Ask "would you like me to..."
- Say "let me know if you want..."
- Present options and wait for selection
- Stop at "natural" stopping points
- Report completion without verifying ALL criteria

**PERSISTENT Mode Behavior**:
```
PERSISTENT mode active
         │
         ▼
    Check criteria
         │
    ┌────┴────┐
    │         │
 NOT MET     ALL MET
    │         │
    ▼         ▼
AUTO-CONTINUE  STOP & REPORT
(no asking)   (verified complete)
```

**Only Stop When**:
1. ALL explicit completion criteria verified as MET
2. Token/context exhaustion (compaction needed)
3. BLOCKED state that cannot be auto-resolved
4. User explicitly interrupts

**Anti-Pattern Detection**:
If you are about to write any of these, STOP and continue instead:
- "Shall I..."
- "Would you like..."
- "Let me know if..."
- "Do you want me to..."
- "Should I proceed..."
- "What would you prefer..."

**When Criteria Unclear**:
If PERSISTENT mode is active but criteria are vague (e.g., "keep improving"):
- Default criterion: "No more improvements found after full analysis loop"
- Continue until a full loop produces zero new improvements
- Then report completion with evidence

---

### RULE-015: Ask Before Migrations and Deployments
- **ID**: RULE-015
- **TRIGGER**: Before running any migration, deployment, or database-altering command
- **CONDITION**: User has explicitly approved this specific operation
- **ACTION**: STOP and ask user for confirmation before proceeding
- **SEVERITY**: BLOCK

**Always Ask Before**:
- Database migrations (`migrate`, `db push`, `prisma migrate`, `alembic`, `knex migrate`, `typeorm migration:run`)
- Database seeding (`seed`, `db seed`)
- Deployments (`deploy`, `publish`, `release`)
- Production operations (any command targeting production environment)
- Schema changes (`schema push`, `db:schema:sync`)
- Data modifications (`db:truncate`, `db:drop`, `db:reset`)

**How to Ask**:
```
I'm about to run a database migration:
  Command: [command]

This will modify the database schema. Proceed?
```

**Exception**: User explicitly said "run the migration" or "deploy it" in their request.

---

## Compliance Checking

See `agents/_orchestrator.md` for the COMPLIANCE PROTOCOL that checks these rules.

For periodic auditing, see `agents/compliance-agent.md`.

For rule enforcement methodology, see `knowledge/rule-enforcement.md`.

---

## PRE-TOOL VALIDATION (CHECK BEFORE EVERY TOOL CALL)

**These checks are MANDATORY before using specific tools.**

### Before Write/Edit on Code Files
```
⛔ HALT if ANY check fails:
  │
  ├─ [ ] Has an agent been spawned for this change?
  │      Check context.md "Agent Contributions" section
  │      If NO → Spawn appropriate agent FIRST
  │
  ├─ [ ] Is planning complete?
  │      Check context.md "Plan" section
  │      If NO → Complete planning FIRST
  │
  └─ [ ] Is this change within the agent's scope?
         If NO → Spawn different agent or expand scope

VIOLATION OUTPUT: "⛔ RULE-001 VIOLATION: Cannot edit [file] without spawning [agent-type]. Halting."
```

### Before Task Tool (Agent Spawn)
```
⛔ HALT if ANY check fails:
  │
  ├─ [ ] Planning phase complete?
  │      workspace/[task-id]/context.md exists with Plan section?
  │      If NO → Create and populate FIRST
  │
  ├─ [ ] Model correctly selected?
  │      • architect-agent → Opus (ALWAYS)
  │      • ticket-analyst-agent → Opus (ALWAYS)
  │      • reviewer-agent → Opus (ALWAYS)
  │      • All others → Sonnet (check escalation triggers)
  │
  ├─ [ ] Agent prompt uses READ pattern?
  │      Says "READ agents/[name].md" NOT pastes content?
  │      If NO → Rewrite prompt
  │
  └─ [ ] Context.md will be updated after agent completes?
         If NO → Add to your plan

VIOLATION OUTPUT: "⛔ RULE-003 VIOLATION: Cannot spawn agent without planning. Halting."
```

### Before Marking Task Complete
```
⛔ HALT if ANY check fails:
  │
  ├─ [ ] Self-reflection performed?
  │      Ran through confidence checklist?
  │      If NO → Perform self-reflection NOW
  │
  ├─ [ ] All verification commands run?
  │      Tests pass? Build succeeds? Linting clean?
  │      If NO → Run verifications FIRST
  │
  ├─ [ ] Confidence level stated?
  │      HIGH/MEDIUM/LOW with reasoning?
  │      If NO → Add confidence assessment
  │
  └─ [ ] All completion criteria verified?
         Every criterion explicitly checked?
         If NO → Check each criterion

VIOLATION OUTPUT: "⛔ RULE-012 VIOLATION: Cannot mark complete without self-reflection. Halting."
```

### After Any Agent Completes
```
⛔ HALT if ANY check fails:
  │
  ├─ [ ] Agent output includes Status field?
  │      Status: COMPLETE | BLOCKED | NEEDS_INPUT
  │      If MISSING → Request status before proceeding
  │
  ├─ [ ] Status is not BLOCKED?
  │      If BLOCKED → Resolve blocker FIRST (don't continue)
  │
  ├─ [ ] Status is not NEEDS_INPUT?
  │      If NEEDS_INPUT → Get user clarification FIRST
  │
  └─ [ ] Context.md updated with agent contribution?
         If NO → Update NOW before next action

VIOLATION OUTPUT: "⛔ RULE-004 VIOLATION: Agent output missing status. Requesting status."
```

---

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
3. Store inputs in `mockups/`, outputs in `outputs/`

See `knowledge/organization.md` for full guidelines.

---

## Planning Phase Protocol

Every task requiring agent delegation MUST go through a mandatory planning phase BEFORE execution.

### When to Plan

Planning is **REQUIRED** when:
- Any agent will be spawned
- Task involves code changes
- Task has 2+ steps
- Multiple domains are involved

Planning is **SKIPPED** only for:
- Pure read-only questions (no code changes)
- Single direct answers (no agents needed)
- Codebase navigation questions

### Planning Phase Steps

#### Step 1: Create Task Workspace
1. Generate task ID (ticket number or `YYYY-MM-DD-description`)
2. Create `workspace/[task-id]/` folder structure
3. Initialize `context.md` from template (include Plan section)

#### Step 2: Run Planning Checklist
Evaluate each domain against the task using criteria from knowledge bases:

| Domain | Criteria (YES if any match) | Knowledge Base | Agent |
|--------|----------------------------|----------------|-------|
| **Testing** | New code, behavior changes, bug fixes, core functionality, user requests tests | `knowledge/testing.md` | test-agent |
| **Documentation** | New API, API changes, config changes, user features, user requests docs | `knowledge/documentation.md` | docs-agent |
| **Security** | Auth, user input, sensitive data, DB queries, HTTP requests, file ops, payments | `knowledge/security.md` | security-agent |
| **Architecture** | New component, boundary changes, design decisions, integrations, unclear scope | `knowledge/architecture.md` | architect-agent |
| **Performance** | Large loops, DB queries, caching, hot paths, async ops, latency requirements | `knowledge/performance.md` | performance-agent |
| **Review** | Code changes ready for merge, user requests review | `knowledge/pr-review.md` | reviewer-agent |
| **Clarity** | Vague request, missing acceptance criteria, unclear scope | `knowledge/ticket-understanding.md` | ticket-analyst-agent |

#### Step 3: Generate Plan
Populate the "Plan" section in `context.md` with:
- Checklist results (which domains needed)
- Decomposed subtasks following three principles:
  - **Solvability**: Each subtask achievable by a single agent
  - **Completeness**: All subtasks together fully address the request
  - **Non-Redundancy**: No overlap between subtasks
- Agent assignments and execution sequence
- Success criteria per subtask

#### Step 4: Approval Gate
- **Plan Mode Active**: Present plan to user, wait for approval
- **Plan Mode Inactive** (default): Auto-proceed to execution

### Plan Mode Toggle

Users can enable/disable plan approval:
- Enable: "enable plan mode", "I want to approve plans"
- Disable: "disable plan mode", "auto-execute"

When plan mode is **ACTIVE**:
- Present plan after generation
- Wait for: "approve", "proceed", "execute"
- Allow modifications before approval

---

## Agent Roster

| Agent | Spawn For |
|-------|-----------|
| `test-agent` | Tests, TDD, coverage |
| `debug-agent` | Bugs, errors, root cause |
| `architect-agent` | Design, architecture |
| `reviewer-agent` | Code reviews |
| `docs-agent` | Documentation |
| `estimator-agent` | Story points |
| `ui-agent` | Frontend, mockups |
| `workflow-agent` | Complex implementations |
| `research-agent` | Web research |
| `security-agent` | Security audits |
| `refactor-agent` | Code cleanup |
| `explore-agent` | Code understanding |
| `performance-agent` | Profiling, optimization |
| `ticket-analyst-agent` | Requirements, scope |
| `compliance-agent` | Rule auditing |
| `browser-agent` | Interactive browser testing, Playwright MCP |
| `evaluator-agent` | Output verification, quality gate |
| `teacher-agent` | Learning assistance, explain why/how, Socratic tutoring |

## Quick Decision Tree

```
User Request
    │
    ├─ Read-only question? ─────────────────► Direct answer (no agents)
    │
    └─ Requires action/code/agents? ────────► PLANNING PHASE (mandatory)
            │
            ├─ Step 1: Create task workspace
            ├─ Step 2: Run Planning Checklist (ALL 7 domains)
            ├─ Step 3: Select MODEL for each agent:
            │          • architect/ticket-analyst/reviewer → Opus
            │          • All others → Sonnet (unless triggers match)
            ├─ Step 4: Generate plan in context.md
            ├─ Step 5: Approval gate (if plan mode active)
            │
            └─ EXECUTION PHASE
                 │
                 ├─ Simple, single domain? ─────────► Spawn 1 agent
                 ├─ Multiple domains, dependent? ───► Sequential agents
                 ├─ Multiple domains, independent? ─► Parallel agents
                 └─ Complex, iterative? ────────────► Collaborative loop
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
model: "[opus|sonnet]"  ← REQUIRED per Model Selection Protocol
prompt: [Include all of the following]
```

**Agent Prompt Template (Token-Efficient)**:

```markdown
## Your Role
You are [agent-name]. READ `agents/[agent-name].md` for your full definition.

## Your Knowledge Base
READ `knowledge/[topic].md` for domain expertise.

## Task Context
Task ID: [task-id]
[If collaborative]: READ `workspace/[task-id]/context.md`

## Your Task
[Specific instructions for this task]

## MANDATORY Output Format (REQUIRED - NO EXCEPTIONS)

Your response MUST end with this EXACT format. Responses missing this format will be REJECTED:

---
**Status**: [COMPLETE | BLOCKED | NEEDS_INPUT]
**Confidence**: [HIGH | MEDIUM | LOW]
**Confidence Reasoning**: [1-2 sentences explaining why]
[If BLOCKED]: **Blocked By**: [What's blocking] | **Need**: [What's needed to unblock]
[If NEEDS_INPUT]: **Question**: [Specific question] | **Options**: [Available choices]
**Handoff Notes**: [Key findings for next agent]
---

WARNING: If you skip this format, the orchestrator will HALT and request it.
```

**Why READ**: Agents have tool access. ~50 tokens to instruct vs ~2000 to paste.

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

## When Direct Handling is Permitted

ONLY handle directly (without agents) if **ALL** conditions are true:

1. Task is a **single question** requiring NO code changes
2. Task requires **NO file modifications**
3. Task can be answered in **ONE response**
4. Task does **NOT** involve: testing, debugging, architecture, review, documentation, estimation, security, or refactoring

**If ANY doubt exists → spawn the appropriate agent.**

Examples of direct handling:
- "What does this function do?" (reading/explaining existing code)
- "Where is the config file?" (simple file location)
- "What's the project structure?" (codebase overview)

Examples requiring agent delegation:
- "Fix this bug" → debug-agent (even if it looks simple)
- "Add a test" → test-agent (even for one test)
- "Is this secure?" → security-agent (always)

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
| API, REST, endpoint, HTTP, versioning, GraphQL | `knowledge/api-design.md` |
| explore, codebase, understand, find, where | `knowledge/code-exploration.md` |
| memory, context, compact, compaction, session | `knowledge/memory-management.md` |
| performance, profiling, optimization, bottleneck, latency | `knowledge/performance.md` |
| logging, metrics, tracing, monitoring, observability | `knowledge/observability.md` |
| error, exception, handling, recovery, retry | `knowledge/error-handling.md` |
| prompt, quality, better, improve, chain of thought | `knowledge/prompting-patterns.md` |
| ticket, requirement, scope, acceptance criteria, clarify, decompose | `knowledge/ticket-understanding.md` |
| browser, playwright, interactive, e2e, click, navigate, test app | `knowledge/playwright.md` |
| reflection, confidence, verify, check output, hallucination | `knowledge/self-reflection.md` |
| file edit, write file, unexpectedly modified, windows error | `knowledge/file-editing-windows.md` |
| error, failure, stuck, blocked, retry, recovery, self-healing | `knowledge/error-recovery.md` |
| context, token, attention, memory, scratchpad, write, select, compress, isolate | `knowledge/context-engineering.md` |
| multi-agent, failure, cascade, coordination, misalignment, handoff | `knowledge/multi-agent-failures.md` |
| tool, MCP, tool definition, API, function, parameter, tool use | `knowledge/tool-design.md` |
| teach, learn, explain, understand, why, how, Socratic, scaffold, tutor | `knowledge/teaching.md` |
| model, opus, sonnet, cost, routing, escalation, agent model | `knowledge/model-selection.md` |

---

## Adding to the System

### New Agent
1. Create `agents/[name]-agent.md` following existing format
2. Add to Agent Roster table above
3. Update `agents/_orchestrator.md` routing logic
4. Run `/update-docs` to regenerate documentation

### New Knowledge Base
1. Create `knowledge/[topic].md`
2. Add to Documentation Router table above
3. Run `/update-docs` to regenerate documentation

### Documentation Auto-Update Rule
**After ANY system change** (new agent, new knowledge base, new command):
- Run `/update-docs` to keep docs/ folder current
- Or manually update docs/README.md with new counts

---

## File Structure

```
ClaudeMemory/
├── CLAUDE.md              # This file (orchestrator)
├── .claude/
│   ├── settings.json      # Permissions, hooks, sandbox config
│   └── commands/          # Slash commands (10 commands)
│       ├── gate.md            # Mandatory compliance gate check
│       ├── spawn-agent.md
│       ├── agent-status.md
│       ├── list-agents.md
│       ├── check-task.md
│       ├── compact-review.md
│       ├── update-docs.md
│       ├── plan-task.md
│       ├── set-mode.md
│       └── check-completion.md
├── agents/                # Agent definitions (18 agents)
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
│   ├── refactor-agent.md
│   ├── explore-agent.md
│   ├── performance-agent.md
│   ├── ticket-analyst-agent.md
│   ├── compliance-agent.md
│   ├── browser-agent.md
│   ├── evaluator-agent.md
│   └── teacher-agent.md
├── workspace/             # Task-organized work area
│   └── [task-id]/         # Per-task folders
│       ├── mockups/
│       ├── outputs/
│       ├── snapshots/
│       └── context.md     # Task context & agent handoffs
├── knowledge/             # Knowledge bases (31 files)
│   ├── testing.md
│   ├── debugging.md
│   ├── documentation.md
│   ├── workflow.md
│   ├── story-pointing.md
│   ├── architecture.md
│   ├── pr-review.md
│   ├── ui-implementation.md
│   ├── organization.md
│   ├── research.md
│   ├── security.md
│   ├── refactoring.md
│   ├── api-design.md
│   ├── code-exploration.md
│   ├── memory-management.md
│   ├── performance.md
│   ├── observability.md
│   ├── error-handling.md
│   ├── prompting-patterns.md
│   ├── ticket-understanding.md
│   ├── completion-verification.md
│   ├── rule-enforcement.md
│   ├── playwright.md
│   ├── self-reflection.md
│   ├── file-editing-windows.md
│   └── model-selection.md
└── docs/                  # Auto-generated (gitignored, run /update-docs to create)
```

---

## Key Principles

1. **Analyze before delegating**: Understand the full request first
2. **Rich context**: Agents perform better with full definitions + knowledge
3. **Coordinate handoffs**: Update task context between sequential agents
4. **Synthesize results**: Combine multi-agent outputs coherently
5. **Fail gracefully**: If an agent fails, summarize and ask for guidance
6. **Per-task isolation**: Each task has its own context file

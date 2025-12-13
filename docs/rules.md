# Rules Reference

## Overview

The system uses 13 machine-readable rules encoded in `CLAUDE.md`. Each rule has:
- **ID**: Unique identifier
- **TRIGGER**: When to check the rule
- **CONDITION**: What must be true
- **ACTION**: What to do if violated
- **SEVERITY**: BLOCK (stop execution) or WARN (log and continue)

## Rule List

### RULE-001: Agent Spawn Required for Code Changes
**Severity**: BLOCK

**Trigger**: Before any Write/Edit tool call on code files

**Condition**: An appropriate agent has been spawned for this task

**Action**: STOP operation, identify and spawn appropriate agent

**Agent Mapping**:
- Testing code → `test-agent`
- Bug fixes → `debug-agent`
- Architecture decisions → `architect-agent`
- Security changes → `security-agent`
- Refactoring → `refactor-agent`

---

### RULE-002: TodoWrite for Multi-Step Tasks
**Severity**: BLOCK

**Trigger**: After identifying task has 2+ steps

**Condition**: TodoWrite has been called with task items

**Action**: STOP and create todo list before proceeding

---

### RULE-003: Planning Phase Required
**Severity**: BLOCK

**Trigger**: Before spawning any agent

**Condition**: `workspace/[task-id]/context.md` exists with Plan section populated

**Action**: STOP and execute planning phase first

---

### RULE-004: Agent Status Validation
**Severity**: BLOCK

**Trigger**: After any agent completes

**Condition**: Agent output contains Status: COMPLETE | BLOCKED | NEEDS_INPUT

**Action**: Request status if missing; do NOT proceed without it

---

### RULE-005: Context Logging Required
**Severity**: BLOCK

**Trigger**: After any agent action or orchestrator decision

**Condition**: `workspace/[task-id]/context.md` updated with contribution

**Action**: STOP and update context.md before continuing

---

### RULE-006: Research Agent for Research Tasks
**Severity**: WARN

**Trigger**: When task involves web search, fact verification, or external info

**Condition**: research-agent spawned (not direct WebSearch/WebFetch)

**Action**: Log warning, consider spawning research-agent

---

### RULE-007: Security Agent for Security Tasks
**Severity**: WARN

**Trigger**: When task involves auth, user input, sensitive data, DB queries, HTTP requests, file operations, payments

**Condition**: security-agent spawned or explicitly consulted

**Action**: Log warning, add security-agent to plan

---

### RULE-008: Token Efficient Agent Spawning
**Severity**: WARN

**Trigger**: When spawning any agent via Task tool

**Condition**: Agent prompt instructs to READ files, not paste content

**Action**: Rewrite prompt to use READ pattern

---

### RULE-009: Browser URL Access Policy
**Severity**: WARN

**Trigger**: Before any browser navigation via Playwright MCP

**Condition**: URL matches access policy

**Action**:
- Localhost/127.0.0.1: AUTO-ALLOW
- OAuth providers: AUTO-ALLOW
- Other external URLs: ASK user

**Auto-Allowed URLs**:
- `localhost:*`, `127.0.0.1:*`, `*.localhost`, `[::1]`
- OAuth: `*.b2clogin.com`, `login.microsoftonline.com`, `accounts.google.com`
- OAuth: `*.auth0.com`, `*.okta.com`, `github.com/login/oauth`

---

### RULE-010: Playwright MCP Tool Usage Required
**Severity**: BLOCK

**Trigger**: When using Playwright for browser interaction

**Condition**: Using `mcp__playwright_*` tools directly (NOT writing code)

**Action**: STOP, use MCP tools instead of writing Playwright code

---

### RULE-011: Windows File Edit Resilience
**Severity**: WARN

**Trigger**: When Edit/Write tool fails with "unexpectedly modified" error

**Condition**: On Windows platform

**Action**:
1. Retry with relative path
2. If fails, use Bash/sed workaround
3. If fails, create new file + rename

See `knowledge/file-editing-windows.md` for details.

---

### RULE-012: Self-Reflection Required
**Severity**: BLOCK

**Trigger**: Before any agent reports COMPLETE status

**Condition**: Agent has performed self-reflection checklist

**Action**: Run self-reflection, include confidence level and reasoning

**Requirements**:
- Every agent MUST self-reflect before finalizing
- Output MUST include confidence level (HIGH/MEDIUM/LOW)
- Output MUST include reasoning for confidence
- LOW confidence + critical task → NEEDS_INPUT instead

See `knowledge/self-reflection.md` for protocol.

---

### RULE-013: Model Selection for Agents
**Severity**: WARN

**Trigger**: When spawning any agent via Task tool

**Condition**: Appropriate model selected for task complexity

**Action**: Select model based on task requirements

**Model Guide**:
| Task Type | Model |
|-----------|-------|
| Architecture, complex reasoning | opus |
| Code review, research, analysis | sonnet |
| Quick lookups, simple exploration | haiku |

**Priority**: Accuracy > Speed > Token Cost

---

## Severity Levels

### BLOCK
Execution must stop if condition is violated. These are critical rules that ensure quality and consistency.

### WARN
Log warning but continue. These are best practices that should be followed but don't break functionality.

## Compliance Checking

The orchestrator performs a compliance protocol before actions:
1. Identify which rules apply to current action
2. Check conditions for each applicable rule
3. For BLOCK rules: stop if violated
4. For WARN rules: log and consider

For periodic auditing on long tasks, spawn `compliance-agent`.

# Agents Reference

## Overview

The system includes 18 specialist agents, each expert in a specific domain. Agents are spawned by the orchestrator via the Task tool and coordinate through per-task context files.

## Agent List

### test-agent
**Domain**: Testing, TDD, coverage

**When to spawn**:
- Writing new tests
- Test-driven development
- Coverage analysis
- Test refactoring

**Knowledge base**: `knowledge/testing.md`

---

### debug-agent
**Domain**: Bug analysis, root cause investigation

**When to spawn**:
- Investigating bugs
- Error analysis
- Root cause identification
- Stack trace analysis

**Knowledge base**: `knowledge/debugging.md`

---

### architect-agent
**Domain**: Design, SOLID principles, patterns

**When to spawn**:
- System design decisions
- Architecture reviews
- Pattern recommendations
- Component design

**Knowledge base**: `knowledge/architecture.md`

---

### reviewer-agent
**Domain**: Code review, PR feedback

**When to spawn**:
- Pull request reviews
- Code quality assessment
- Best practices enforcement
- Feedback generation

**Knowledge base**: `knowledge/pr-review.md`

---

### docs-agent
**Domain**: Documentation writing

**When to spawn**:
- Writing documentation
- API docs
- README updates
- Code comments

**Knowledge base**: `knowledge/documentation.md`

---

### estimator-agent
**Domain**: Story pointing, effort estimation

**When to spawn**:
- Sprint planning
- Effort estimation
- Complexity assessment
- Task breakdown

**Knowledge base**: `knowledge/story-pointing.md`

---

### ui-agent
**Domain**: Frontend, UI implementation

**When to spawn**:
- UI component building
- CSS/styling work
- Frontend implementation
- Mockup to code

**Knowledge base**: `knowledge/ui-implementation.md`

---

### workflow-agent
**Domain**: Complex task execution

**When to spawn**:
- Multi-step implementations
- Complex workflows
- Orchestrated changes
- Migration tasks

**Knowledge base**: `knowledge/workflow.md`

---

### research-agent
**Domain**: Web research, verification

**When to spawn**:
- Fact checking
- Technology research
- Best practice lookup
- External information gathering

**Knowledge base**: `knowledge/research.md`

---

### security-agent
**Domain**: Security review, OWASP

**When to spawn**:
- Security audits
- Vulnerability assessment
- Auth/authz changes
- Sensitive data handling

**Knowledge base**: `knowledge/security.md`

---

### refactor-agent
**Domain**: Code smells, cleanup

**When to spawn**:
- Code refactoring
- Technical debt reduction
- Code smell removal
- Structure improvement

**Knowledge base**: `knowledge/refactoring.md`

---

### explore-agent
**Domain**: Codebase exploration

**When to spawn**:
- Understanding code
- Finding implementations
- Codebase navigation
- Architecture discovery

**Knowledge base**: `knowledge/code-exploration.md`

---

### performance-agent
**Domain**: Profiling, optimization

**When to spawn**:
- Performance analysis
- Bottleneck identification
- Optimization work
- Load testing

**Knowledge base**: `knowledge/performance.md`

---

### ticket-analyst-agent
**Domain**: Requirements clarification

**When to spawn**:
- Vague requirements
- Scope definition
- Acceptance criteria
- Task decomposition

**Knowledge base**: `knowledge/ticket-understanding.md`

---

### compliance-agent
**Domain**: Rule auditing

**When to spawn**:
- Periodic rule checks
- Compliance verification
- Long-running tasks
- Policy enforcement

**Knowledge base**: `knowledge/rule-enforcement.md`

---

### browser-agent
**Domain**: Interactive browser testing

**When to spawn**:
- E2E testing
- Browser automation
- UI verification
- Flow testing

**Knowledge base**: `knowledge/playwright.md`

---

### evaluator-agent
**Domain**: Output verification, quality gate

**When to spawn**:
- Verifying agent output quality
- Planner-Worker-Evaluator patterns
- Quality scoring
- Output validation

**Knowledge bases**: `knowledge/self-reflection.md`, `knowledge/error-recovery.md`

---

### teacher-agent
**Domain**: Learning assistance, Socratic tutoring

**When to spawn**:
- User asks to understand "why" or "how"
- Explaining complex decisions
- After agent work, to help user learn
- User wants guided learning, not just answers

**Knowledge base**: `knowledge/teaching.md`

---

## Shared Output Format

All agents use the format defined in `agents/_shared-output.md`:

```markdown
## Self-Reflection
[Task alignment, assumptions, errors, confidence]

## Agent Status
**Status**: [COMPLETE | BLOCKED | NEEDS_INPUT]
**Confidence**: [HIGH | MEDIUM | LOW]
**Confidence Reasoning**: [Why this level]

**Handoff Notes**: [Key findings for next agent]

## Context Acknowledgment
- **Context Read**: YES / NO / N/A
- **Prior Agents**: [List]
- **Key Context Used**: [Summary]
```

## Spawning Agents

The orchestrator spawns agents via the Task tool:

```markdown
## Your Role
You are [agent-name]. READ `agents/[agent-name].md` for your full definition.

## Your Knowledge Base
READ `knowledge/[topic].md` for domain expertise.

## Task Context
Task ID: [task-id]
READ `workspace/[task-id]/context.md`

## Your Task
[Specific instructions]

## Required Output
[Format requirements]
```

## Agent Collaboration

### Conflict Resolution
When agents disagree, priority order:
1. security-agent (security concerns override)
2. architect-agent (design decisions)
3. Domain expert for specific area
4. Orchestrator synthesizes if needed

### Handoff Protocol
1. Agent completes work
2. Writes findings to context.md
3. Reports status (COMPLETE/BLOCKED/NEEDS_INPUT)
4. Orchestrator routes to next agent or synthesizes

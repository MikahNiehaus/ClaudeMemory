# Specialist Agents

> 14 specialist agents available for task delegation

## Agent Roster

| Agent | Expertise | Knowledge Base | Use For |
|-------|-----------|----------------|---------|
| `test-agent` | Testing, TDD, coverage | testing.md | Writing tests, test strategy |
| `debug-agent` | Bug analysis, root cause | debugging.md | Errors, debugging |
| `architect-agent` | Design, SOLID, patterns | architecture.md | Architecture decisions |
| `reviewer-agent` | PR review, feedback | pr-review.md | Code reviews |
| `docs-agent` | Documentation | documentation.md | Writing docs |
| `estimator-agent` | Story points, estimation | story-pointing.md | Ticket estimation |
| `ui-agent` | UI implementation | ui-implementation.md | Frontend, mockups |
| `workflow-agent` | Execution, process | workflow.md | Complex implementations |
| `research-agent` | Web research, verification | research.md | Deep research, fact-checking |
| `security-agent` | Security review, OWASP | security.md | Security audits, vulnerability review |
| `refactor-agent` | Code smells, refactoring | refactoring.md | Code cleanup, technical debt |
| `explore-agent` | Codebase exploration | code-exploration.md | Understanding code, finding patterns |
| `performance-agent` | Profiling, optimization | performance.md | Performance issues, bottlenecks |
| `ticket-analyst-agent` | Requirements, clarification | ticket-understanding.md | Vague requests, scope definition |

## Decision Guide

### Single Domain Tasks
- Testing needed → `test-agent`
- Bug to fix → `debug-agent`
- Design question → `architect-agent`
- Code review → `reviewer-agent`
- Documentation → `docs-agent`
- Estimation → `estimator-agent`
- UI work → `ui-agent`
- Security concern → `security-agent`
- Refactoring → `refactor-agent`
- Understanding code → `explore-agent`
- Performance issue → `performance-agent`
- Vague/unclear request → `ticket-analyst-agent`
- Need external info → `research-agent`

### Multi-Domain Tasks
Use sequential or parallel agents as appropriate:
- Bug fix + tests → `debug-agent` → `test-agent`
- Feature + security → `architect-agent` + `security-agent` (parallel)
- Vague request → `ticket-analyst-agent` → appropriate specialist

## Agent Output Status

All agents report one of:
- **COMPLETE**: Task finished successfully
- **BLOCKED**: Cannot proceed (explain why)
- **NEEDS_INPUT**: Requires user clarification

## Spawning Agents

Use `/spawn-agent <agent-name> <task-id>` or delegate through the orchestrator naturally.

Agents receive:
1. Full agent definition from `agents/[name].md`
2. Full knowledge base from `knowledge/[topic].md`
3. Task context from `workspace/[task-id]/context.md`

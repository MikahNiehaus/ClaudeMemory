# Knowledge Bases Reference

## Overview

30 knowledge bases in `knowledge/` provide domain expertise for agents and the orchestrator. Each has TRIGGER keywords for automatic routing.

## Knowledge Base List

| File | Domain | Triggers |
|------|--------|----------|
| `testing.md` | Test Writing | test, TDD, mock, assert |
| `debugging.md` | Bug Fixing | debug, error, bug, fix |
| `documentation.md` | Code Documentation | document, docstring, README |
| `workflow.md` | Development Workflow | implement, execute, workflow |
| `story-pointing.md` | Story Estimation | estimate, story point, sprint |
| `architecture.md` | Software Architecture | architecture, SOLID, pattern |
| `pr-review.md` | PR Review | review, PR, pull request |
| `ui-implementation.md` | UI Implementation | UI, mockup, frontend, Figma |
| `organization.md` | Workspace Organization | organize, workspace, task folder |
| `research.md` | Web Research | research, search, verify, citation |
| `security.md` | Application Security | security, OWASP, vulnerability, XSS |
| `refactoring.md` | Code Refactoring | refactor, code smell, technical debt |
| `api-design.md` | API Design | API, REST, endpoint, HTTP, GraphQL |
| `code-exploration.md` | Code Exploration | explore, codebase, understand, find |
| `memory-management.md` | Memory & Context | memory, context, compact, session |
| `performance.md` | Performance Optimization | performance, profiling, bottleneck |
| `observability.md` | Observability | logging, metrics, tracing, monitoring |
| `error-handling.md` | Error Handling | error, exception, handling, recovery |
| `prompting-patterns.md` | Quality Patterns | prompt, quality, better, improve |
| `ticket-understanding.md` | Ticket Analysis | ticket, requirement, scope, criteria |
| `completion-verification.md` | Task Completion | completion, verify, done, criteria |
| `rule-enforcement.md` | Rule Compliance | rule, enforce, compliance, violation |
| `playwright.md` | Playwright MCP | browser, playwright, interactive, e2e |
| `self-reflection.md` | Agent Self-Reflection | reflection, confidence, hallucination |
| `file-editing-windows.md` | Windows File Editing | file edit, unexpectedly modified |
| `context-engineering.md` | Context Engineering | context, write, select, compress, isolate |
| `error-recovery.md` | Error Recovery | error, recovery, detect, decide, act |
| `multi-agent-failures.md` | Multi-Agent Failures | agent, failure, cascade, MAST |
| `tool-design.md` | Tool Design | tool, MCP, function, parameter, API |
| `teaching.md` | Teaching & Pedagogy | teach, learn, explain, Socratic, scaffold |

## Knowledge Base Categories

### Software Engineering
- `testing.md` - TDD, test patterns, coverage
- `debugging.md` - Root cause analysis, debugging techniques
- `architecture.md` - SOLID, Clean Architecture, patterns
- `refactoring.md` - Code smells, refactoring catalog
- `api-design.md` - REST, GraphQL, HTTP semantics

### Security
- `security.md` - OWASP Top 10, secure coding

### Operations
- `performance.md` - Profiling, optimization
- `observability.md` - Logging, metrics, tracing
- `error-handling.md` - Error design, recovery patterns

### Process
- `workflow.md` - Development workflow
- `pr-review.md` - Code review practices
- `documentation.md` - Documentation writing
- `story-pointing.md` - Estimation techniques
- `ticket-understanding.md` - Requirements analysis

### UI/UX
- `ui-implementation.md` - Frontend implementation

### System-Specific
- `organization.md` - Workspace organization
- `memory-management.md` - Context persistence
- `completion-verification.md` - Task completion
- `rule-enforcement.md` - Compliance checking
- `self-reflection.md` - Anti-hallucination
- `file-editing-windows.md` - Windows bug workarounds
- `playwright.md` - Browser testing setup
- `context-engineering.md` - Four pillars of context management
- `error-recovery.md` - Agent error handling framework
- `multi-agent-failures.md` - MAST taxonomy, failure prevention
- `tool-design.md` - Tool definition best practices

### Research
- `research.md` - Web research methodology
- `code-exploration.md` - Codebase navigation
- `prompting-patterns.md` - Prompt engineering

### Education
- `teaching.md` - Socratic tutoring, metacognitive scaffolding

## Usage

### By Orchestrator
Keyword-based routing for simple lookups:
```
User mentions "test" → Read knowledge/testing.md
User mentions "security" → Read knowledge/security.md
```

### By Agents
Agents READ their knowledge base before acting:
```markdown
## Your Knowledge Base
READ `knowledge/testing.md` for domain expertise.
```

### Direct Access
Users can ask for specific documentation:
```
"What are the testing best practices?"
→ Orchestrator reads knowledge/testing.md
```

## Structure

Each knowledge base follows a pattern:
1. **TRIGGER** line with keywords
2. **Overview** section
3. **Best Practices** / **Checklists**
4. **Examples** / **Patterns**
5. **Anti-Patterns** to avoid
6. **Quick Reference** / **Summary**

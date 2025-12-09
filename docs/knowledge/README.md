# Knowledge Bases

> 19 knowledge bases providing domain expertise

## Knowledge Base Registry

| File | Domain | Trigger Keywords |
|------|--------|------------------|
| testing.md | Test Writing | test, TDD, mock, assert |
| debugging.md | Bug Fixing | debug, error, bug, fix |
| documentation.md | Code Documentation | document, docstring, README |
| workflow.md | Development Workflow | implement, execute, workflow |
| story-pointing.md | Story Estimation | estimate, story point, sprint |
| architecture.md | Software Architecture | architecture, SOLID, pattern |
| pr-review.md | PR Review | review, PR, pull request |
| ui-implementation.md | UI Implementation | UI, mockup, frontend, Figma |
| organization.md | Workspace Organization | organize, workspace, task folder |
| research.md | Web Research | research, search, verify, citation |
| security.md | Application Security | security, OWASP, vulnerability, XSS |
| refactoring.md | Code Refactoring | refactor, code smell, technical debt |
| api-design.md | API Design | API, REST, endpoint, HTTP, GraphQL |
| code-exploration.md | Code Exploration | explore, codebase, understand, where |
| memory-management.md | Memory & Context | memory, context, compact, session |
| performance.md | Performance Optimization | performance, profiling, bottleneck |
| observability.md | Observability | logging, metrics, tracing, monitoring |
| error-handling.md | Error Handling | error, exception, recovery, retry |
| prompting-patterns.md | Quality Patterns | prompt, quality, improve, chain of thought |
| ticket-understanding.md | Ticket Analysis | ticket, requirement, scope, acceptance criteria |

## How Knowledge Bases Work

1. **Automatic Loading**: When an agent is spawned, its associated knowledge base is included in the prompt
2. **On-Demand Reference**: The orchestrator can read knowledge bases for quick lookups without spawning agents
3. **Trigger-Based Routing**: Keywords in user requests help identify relevant knowledge bases

## Knowledge Base Structure

Each knowledge base follows a consistent format:
- **TRIGGER line**: When to use this documentation
- **Core concepts**: Fundamental principles
- **Methodologies**: Step-by-step approaches
- **Checklists**: Actionable verification lists
- **Examples**: Practical demonstrations
- **Anti-patterns**: What to avoid

## Adding New Knowledge

1. Create `knowledge/[topic].md` following the structure above
2. Add to Documentation Registry in MEMORY.md
3. Add to router table in CLAUDE.md
4. Consider creating a matching agent
5. Run `/update-docs`

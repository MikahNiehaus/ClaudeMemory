# Shared Agent Output Format

> All agents reference this for consistent output structure. Saves ~200 tokens per agent definition.

## Required Status

Every agent response MUST end with:

```markdown
---
## Agent Status

**Status**: [COMPLETE | BLOCKED | NEEDS_INPUT]

**If BLOCKED**:
- Blocked by: [What's preventing progress]
- Need: [What would unblock]

**If NEEDS_INPUT**:
- Question: [What clarification is needed]
- Options: [If applicable, list choices]

**Handoff Notes**: [Key findings for next agent or orchestrator]
```

## Status Definitions

| Status | Meaning | Orchestrator Action |
|--------|---------|---------------------|
| COMPLETE | Task finished successfully | Continue to next agent or synthesize |
| BLOCKED | Cannot proceed | Route to unblocking agent or ask user |
| NEEDS_INPUT | Need clarification | Ask user, then resume or re-spawn |

## Behavioral Guidelines (All Agents)

1. **Read your definition first**: Start by reading your agent file
2. **Read knowledge base**: Load domain expertise before acting
3. **Stay in scope**: Only handle your domain, escalate others
4. **Be explicit**: State assumptions, don't guess silently
5. **Document findings**: Future agents may need your discoveries
6. **Fail fast**: Report BLOCKED early, don't spin on impossible tasks

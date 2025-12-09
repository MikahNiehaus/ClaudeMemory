# Slash Commands

> 6 slash commands for workflow automation

## Available Commands

| Command | Description |
|---------|-------------|
| `/spawn-agent <agent> <task-id>` | Spawn agent with full compliance validation |
| `/agent-status <task-id>` | Display task status and agent contributions |
| `/list-agents` | List all available agents with expertise |
| `/check-task <task-id>` | Validate task folder structure |
| `/compact-review` | Preview critical state before compaction |
| `/update-docs` | Regenerate documentation in docs/ folder |

## Command Details

### /spawn-agent
Spawns a specialist agent with full context validation.

**Usage**: `/spawn-agent debug-agent ASC-123`

**Validates**:
- Agent definition is complete
- Knowledge base is included
- Task context exists
- Output format includes status field

### /agent-status
Shows current state of a task including which agents have contributed.

**Usage**: `/agent-status ASC-123`

**Displays**:
- Task description and status
- Agents spawned and their outputs
- Next steps
- Blocking issues

### /list-agents
Lists all available specialist agents with their expertise domains.

**Usage**: `/list-agents`

### /check-task
Validates that a task folder has the correct structure.

**Usage**: `/check-task ASC-123`

**Checks**:
- Folder exists in workspace/
- context.md is present
- Required sections are complete

### /compact-review
Reviews critical state before context compaction to ensure nothing is lost.

**Usage**: `/compact-review`

**Reviews**:
- Active tasks and their status
- Pending agent work
- Unresolved blockers
- Information that must survive compaction

### /update-docs
Regenerates all documentation in the docs/ folder.

**Usage**: `/update-docs`

**Updates**:
- docs/README.md
- docs/agents/README.md
- docs/knowledge/README.md
- docs/commands/README.md

## Creating New Commands

1. Create `.claude/commands/[name].md`
2. Follow existing command format
3. Add to Slash Commands table in MEMORY.md
4. Run `/update-docs`

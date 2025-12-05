# Slash Commands

5 workflow automation commands for the orchestration system.

## Command List

| Command | Description |
|---------|-------------|
| `/spawn-agent <agent> <task-id>` | Spawn agent with full compliance validation |
| `/agent-status <task-id>` | Display task status and agent contributions |
| `/list-agents` | List all available agents with expertise |
| `/check-task <task-id>` | Validate task folder structure |
| `/compact-review` | Preview critical state before compaction |
| `/update-docs` | Regenerate documentation |

## Usage Examples

### Spawn an Agent
```
/spawn-agent debug-agent BUG-123
```
Validates agent definition exists, creates task workspace, spawns with full context.

### Check Task Status
```
/agent-status BUG-123
```
Shows current status, agent contributions, next steps, blockers.

### List Available Agents
```
/list-agents
```
Displays all 13 agents with their expertise and when to use them.

### Validate Task Structure
```
/check-task BUG-123
```
Checks workspace folder, context.md, MEMORY.md registration.

### Pre-Compaction Review
```
/compact-review
```
Shows critical state that needs to survive context compaction.

### Update Documentation
```
/update-docs
```
Regenerates docs/ folder with current system state.

---
*Last updated: 2025-12-05*

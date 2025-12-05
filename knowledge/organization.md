# Workspace Organization

TRIGGER: organize, workspace, task folder, artifact, snapshot, context

## Overview

All task-related artifacts should be organized in the `workspace/` folder using task-specific subfolders. Each task has its own isolated context for agent collaboration.

## Folder Structure

```
workspace/
└── [task-id]/
    ├── mockups/       # Design references, input images, specifications
    ├── outputs/       # Generated artifacts, final deliverables
    ├── snapshots/     # Screenshots, progress captures, debugging images
    └── context.md     # Task context, notes, agent contributions, handoffs
```

## Task ID Naming Convention

### When Ticket Number Exists
Use the ticket number directly:
- `ASC-914`
- `JIRA-123`
- `GH-456`
- `BUG-789`

### When No Ticket Number
Auto-generate using format: `YYYY-MM-DD-short-description`
- `2025-12-04-auth-refactor`
- `2025-12-04-fix-login-bug`
- `2025-12-04-add-caching`

Keep descriptions short (2-4 words), lowercase, hyphen-separated.

## What Goes Where

### mockups/
- Design mockups and wireframes
- UI specifications
- Reference images
- Input files for the task

### outputs/
- Generated code snippets
- Final deliverables
- Exported reports
- Completed artifacts

### snapshots/
- Screenshots during testing
- Progress captures
- Debugging images
- Before/after comparisons

### context.md
- Task status and description
- Notes and findings
- Agent contributions and handoffs
- Open questions
- Session history

## context.md Template

Every task folder should have a `context.md` file:

```markdown
# Task: [Task ID]

## Status
- **State**: [ACTIVE/BLOCKED/COMPLETE]
- **Current Phase**: [Phase name or description]
- **Blocked By**: [If blocked, what's stopping progress]

## Task Description
[What this task is about, requirements, goals]

## Notes & Findings
[Human notes, discoveries, key decisions made]

## Agent Contributions

### [Agent Name] - [Timestamp]
- **Task**: What agent was asked to do
- **Status**: COMPLETE/BLOCKED/NEEDS_INPUT
- **Key Findings**: Main discoveries
- **Output**: What was produced
- **Handoff Notes**: What next agent needs to know

## Handoff Queue
| Next Agent | Reason | Priority |
|------------|--------|----------|
| [agent]    | [why]  | [P0-P2]  |

## Open Questions
- [ ] Question 1
- [ ] Question 2

## Session History
| Time | Agent | Action | Result |
|------|-------|--------|--------|
| - | - | - | - |
```

## Context Lifecycle

### When to Create context.md
Create when starting a task that:
- Involves multiple steps
- Will have multiple agents collaborate
- Spans multiple sessions
- Needs progress tracking

### When to Update context.md
Update after:
- Each agent completes work (add to Agent Contributions)
- Key decisions are made (add to Notes & Findings)
- Status changes (update State)
- Questions arise (add to Open Questions)

### State Transitions
```
ACTIVE → Working on task, agents can contribute
BLOCKED → Cannot proceed, see "Blocked By" field
COMPLETE → Task finished, final summary in Notes
```

### When to Archive
- Mark COMPLETE when task is done
- Keep folder for reference if artifacts may be needed
- Clean up during project completion

## When to Create Task Folders

**DO create a task folder when:**
- Task involves multiple steps
- You'll generate artifacts (screenshots, outputs)
- Multiple agents will collaborate
- Work spans multiple sessions
- You need to track progress

**DON'T need a task folder for:**
- Quick questions
- Simple file reads
- One-shot explanations
- No artifacts generated

## Workflow Integration

### Before Starting Work
1. Determine task ID (ticket number or generate one)
2. Create `workspace/[task-id]/` folder structure
3. Create `context.md` from template
4. Register task in `MEMORY.md` Active Tasks table
5. Store any input materials in `mockups/`

### During Work
1. Save screenshots to `snapshots/`
2. Update `context.md` with findings and agent contributions
3. Store generated artifacts in `outputs/`
4. Update task status in `MEMORY.md`

### After Completion
1. Ensure all artifacts are in proper folders
2. Update `context.md` state to COMPLETE
3. Add final summary to Notes & Findings
4. Mark task complete in `MEMORY.md`
5. Clean up any temporary files

## Cleanup Guidelines

### Active Tasks
- Keep all folders for active/recent tasks
- Context provides collaboration history

### Completed Tasks
- Keep for reference if artifacts may be needed later
- Archive or remove after project completion
- Final summary should be in context.md before cleanup

### Root Folder Rules
- Keep workspace root clean
- Only `.gitkeep` at root level
- All task work in subfolders

## Example Task Setup

```
# For ticket ASC-914 (UI button fix)
workspace/
└── ASC-914/
    ├── mockups/
    │   └── button-design.png
    ├── outputs/
    │   └── button-component.tsx
    ├── snapshots/
    │   ├── before-fix.png
    │   └── after-fix.png
    └── context.md

# For ad-hoc refactoring task
workspace/
└── 2025-12-04-auth-refactor/
    ├── mockups/
    ├── outputs/
    │   └── auth-service.ts
    ├── snapshots/
    └── context.md
```

## Integration with Multi-Agent System

When agents collaborate on a task:
1. All agents reference the same task folder
2. Each agent reads/updates `context.md` for handoffs
3. Agent contributions are logged with timestamps
4. Artifacts are accessible to all collaborating agents
5. Orchestrator updates context after each agent completes

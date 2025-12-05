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

## Quick Resume
[1-2 sentences: Current state for session recovery after compaction]
Example: "Implementing auth refactor. debug-agent found root cause in token.ts:45, waiting for test-agent to write regression tests."

## Status
- **State**: [ACTIVE/BLOCKED/COMPLETE]
- **Current Phase**: [Phase name or description]
- **Last Agent**: [Most recent agent that contributed]
- **Created**: [YYYY-MM-DD]
- **Updated**: [YYYY-MM-DD]

## Blocked Resolution (if BLOCKED)
- **Blocked By**: [Specific blocker description]
- **To Unblock**: [Required action or input needed]
- **Owner**: [Who needs to act: user/specific-agent/external]
- **Attempted**: [What has been tried so far]

## Key Files
[Critical files for this task - read these first when resuming]
- `path/to/file1.ts` - [why important]
- `path/to/file2.ts` - [why important]

## Task Description
[What this task is about, requirements, goals]

## Orchestrator Decisions

### Request Analysis - [Timestamp]
- **User Request**: [Original request]
- **Domains Identified**: [testing, debugging, architecture, etc.]
- **Agents Considered**: [List all agents that could apply]
- **Agents Spawned**: [List agents actually spawned and WHY]
- **Rationale**: [Why these specific agents]

## Notes & Findings
[Human notes, discoveries, key decisions made]

## Agent Contributions

### [Agent Name] - [Timestamp]
- **Task**: What agent was asked to do
- **Status**: COMPLETE/BLOCKED/NEEDS_INPUT
- **Key Findings**: Main discoveries
- **Output**: What was produced (or path to outputs/ folder)
- **Handoff Notes**: What next agent needs to know

## Handoff Queue
| Next Agent | Reason | Priority |
|------------|--------|----------|
| [agent]    | [why]  | [P0-P2]  |

## Open Questions
- [ ] Question 1
- [ ] Question 2

## Next Steps
1. [First thing to do when resuming]
2. [Second thing to do]
3. [Third thing to do]

## Session History
| Time | Agent | Action | Result |
|------|-------|--------|--------|
| - | - | - | - |
```

## Status Definitions

### Task Status (in MEMORY.md / context.md)

| Status | Meaning | Actions |
|--------|---------|---------|
| **ACTIVE** | Work in progress | Continue with next steps |
| **BLOCKED** | Cannot proceed | Check "Blocked Resolution" section |
| **COMPLETE** | Task finished | Archive or clean up |

### Agent Status (in agent outputs)

| Status | Meaning | Orchestrator Action |
|--------|---------|---------------------|
| **COMPLETE** | Agent finished successfully | Continue to next agent or synthesize |
| **BLOCKED** | Cannot proceed | Check blocker, route to unblocking agent or ask user |
| **NEEDS_INPUT** | Requires user clarification | Present question to user |

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

**ALWAYS create a task folder when:**
- Task involves ANY code changes
- Task involves multiple steps
- ANY agent is being spawned
- You'll generate artifacts (screenshots, outputs)
- Multiple agents will collaborate
- Work spans multiple sessions
- You need to track progress

**ONLY skip task folder for:**
- Single read-only questions (no file modifications)
- Simple explanations that fit in one response
- Codebase navigation questions

**When in doubt → create the folder.**

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

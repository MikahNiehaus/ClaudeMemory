---
argument-hint: <task-id>
description: Validate task folder structure and completeness
allowed-tools: Read, Glob
---

# Task Validation: $ARGUMENTS

## Instructions

Validate the task workspace at `workspace/$1/` for completeness and correctness.

## Validation Checklist

### 1. Structure Check
Verify these directories/files exist:
- [ ] `workspace/$1/` - Task root folder
- [ ] `workspace/$1/context.md` - Task context file
- [ ] `workspace/$1/mockups/` - Input designs (optional)
- [ ] `workspace/$1/outputs/` - Generated artifacts (optional)
- [ ] `workspace/$1/snapshots/` - Progress screenshots (optional)

### 2. Context.md Validation
Check that context.md contains required sections:
- [ ] **Task ID** and description
- [ ] **Status** (ACTIVE/BLOCKED/COMPLETE)
- [ ] **Created** timestamp
- [ ] **Agent Contributions** section
- [ ] **Key Findings** section
- [ ] **Next Steps** section

### 3. MEMORY.md Registration
- [ ] Task is listed in Active Tasks table
- [ ] Status matches context.md status
- [ ] Workspace path is correct

### 4. Agent Output Compliance
For each agent contribution in context.md:
- [ ] Status field present (COMPLETE/BLOCKED/NEEDS_INPUT)
- [ ] If BLOCKED, blocker is documented
- [ ] Handoff notes present for sequential agents

## Output Format

```
Task: $1
─────────────────────────────
Structure:     [PASS/FAIL] - [details]
Context.md:    [PASS/FAIL] - [missing sections]
MEMORY.md:     [PASS/FAIL] - [registration status]
Agent Outputs: [PASS/FAIL] - [compliance issues]
─────────────────────────────
Overall:       [VALID/INVALID]
Issues:        [list of issues to fix]
```

## Task Files
@workspace/$1/context.md

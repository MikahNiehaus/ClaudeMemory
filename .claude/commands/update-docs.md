---
description: Regenerate documentation in docs/ folder
allowed-tools: Read, Write, Glob
---

# Update Documentation

Regenerate all documentation in the `docs/` folder to reflect current system state.

## Instructions

1. **Read current state** from MEMORY.md:
   - Agent count and list
   - Knowledge base count and list
   - Slash command list
   - Recent session history

2. **Update docs/README.md**:
   - Update counts
   - Update date
   - Sync with MEMORY.md

3. **Update docs/agents/README.md**:
   - List all agents from agents/ folder
   - Update agent table
   - Update decision guide

4. **Update docs/knowledge/README.md**:
   - List all knowledge bases from knowledge/ folder
   - Update triggers table

5. **Update docs/commands/README.md**:
   - List all commands from .claude/commands/
   - Update usage examples

6. **Create individual doc files** (if missing):
   - For each agent: docs/agents/[name].md
   - For each knowledge base: docs/knowledge/[name].md

## After Update

Report:
- Files updated
- New files created
- Current counts (agents, knowledge bases, commands)
- Last updated timestamp

@MEMORY.md

# Task: MCP RAG Server for ClaudeMemory

## Task ID
2026-03-31-mcp-rag-server

## Goal
Design and implement a local MCP server that provides semantic search (RAG) over:
- 33 knowledge bases (knowledge/*.md)
- 21 agent definitions (agents/*.md)
- Historical workspace contexts (workspace/*/context.md)
- Target project codebases (user's actual project files)

## Why
Current knowledge retrieval is manual and explicit — the orchestrator hard-codes which files agents read. RAG enables cross-cutting discovery, historical learning, and codebase-aware agents.

## Status
- [x] Workspace created
- [ ] Architecture plan (architect-agent)
- [ ] Implementation
- [ ] Testing
- [ ] Integration with ClaudeMemory orchestrator

## Agent Log
| Agent | Status | Output |
|-------|--------|--------|
| architect-agent | PENDING | - |

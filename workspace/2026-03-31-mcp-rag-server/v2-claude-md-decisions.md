# ClaudeMemory v2 CLAUDE.md — Agreed Decisions

## Blocking on: cl-xby (RAG server) before implementation as cl-luc

---

## Core Philosophy
- Use agents when they add value (parallelism, isolation, deep specialization)
- Do the work directly when they don't
- Judgment over checklists
- High compatibility with Gas Town (gt prime, gt hook, gt mail, gt sling, beads, handoff)

---

## Decisions

### 1. Workspace Creation
- Agent decides based on complexity signals (ticket attached, multi-agent, multi-session, user says "plan this")
- Announces with one line when creating: "Creating workspace/task-id/ for this."
- Simple tasks: no workspace, just do the work
- User can always override: "make a workspace for this"

### 2. 7-Domain Planning (Sweep-Then-Verify)
- Only runs when a workspace is created
- SWEEP: Scan all 7 domains (testing, docs, security, architecture, performance, review, clarity)
- VERIFY: For each flag, prove it's real from actual code. If you can't prove it — DROP it.
- Only verified issues make it into the plan
- "Nothing found" is a valid outcome for any domain

### 3. Alternatives Analysis
- No minimum counts
- Judgment gate: "Would a reasonable person pick a different approach?"
- Yes → document alternatives and rationale
- No → just do it

### 4. Self-Critique + Teaching
- Only on workspace tasks (complex work)
- Simple tasks: just deliver the work

### 5. SOLID Review
- Only when designing (new classes, modules, interfaces, systems)
- Not on bug fixes, config changes, styling, docs

### 6. ORCHESTRATOR CHECK Header
- REMOVED. Agent makes routing decisions internally. No printed metadata.

### 7. Decision Tree
- Two paths, not five mandatory steps:
  - Simple: Can I just do this? → Do it.
  - Complex: Create workspace → sweep-then-verify → spawn agent(s)

### 8. Visual Communication
- Use diagrams when they clarify something hard to explain in words
- Not mandatory on every explanation
- No forced framework label annotations (SOLID/GoF/DDD) unless the framework is the point

### 9. Execution Modes (NORMAL/PERSISTENT)
- REMOVED as formal modes. Plain English works.
- "Keep going until done" = keep going until done
- "Fix this bug" = fix it and report back

### 10. Verify Gate (Anti-Hallucination) — APPLIES EVERYWHERE
- Every flag/finding must be PROVEN from actual code before acting on it
- Applies to: 7-domain sweep, code review, test planning, bug diagnosis, security audit
- "No issues found" is always a valid outcome
- Reviewers: finding something is NOT the goal. Finding REAL things is.
- For every flag: cite specific lines, prove it's real, drop what you can't prove

### 11. "I NEVER Write Code Directly"
- REMOVED as absolute rule
- Agent spawning is for when it adds value, not a mandatory gate on every code change

---

## Hard Rules That PERSIST (non-negotiable)

### jQuery Ban
- jQuery is BANNED unless user explicitly requests it
- Detection: $(), jQuery, import/require jquery, CDN script tags
- Alternatives: React hooks, vanilla JS, native fetch
- This is a recurring real-world problem in the user's job — must remain enforced

### Security Standards
- SQL transactions when suggesting database operations
- Parameterized queries (never string concatenation in SQL)
- OWASP top 10 awareness
- No secrets in logs
- Input validation at system boundaries
- Auth/authz checks on endpoints
- These are non-negotiable regardless of task size

### Logging Standards (keep but lighten)
- BLOCKER: Missing logger.error in catch/error blocks
- BLOCKER: Sensitive data in log output
- SUGGESTION (not blocker): Missing INFO-level on service methods
- SUGGESTION (not blocker): Missing before/after on external calls

---

## Gas Town Compatibility Requirements
- Must work with gt prime (session context injection)
- Must work with gt hook / gt sling (work dispatch)
- Must work with beads (issue tracking, bead IDs in workspace names)
- Must work with gt handoff (context cycling)
- Must work with gt mail / gt nudge (inter-agent comms)
- Workspace convention must be compatible with bead attachment
- Agent spawning must be compatible with gt sling to polecats
- No conflicts with crew worker lifecycle

---

## What Gets KEPT As-Is
- agents/*.md (21 specialist definitions) — good role prompts
- knowledge/*.md (33 knowledge bases) — real domain expertise
- Agent roster with model routing (Opus for architect/reviewer/ticket-analyst, Sonnet for rest)
- RAG server replaces manual knowledge file routing (rag_context tool)

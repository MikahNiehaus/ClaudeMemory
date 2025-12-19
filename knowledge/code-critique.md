# Code Self-Critique Protocol

TRIGGER: critique, self-review, code review, line-by-line, assumptions, edge cases, trade-offs

## Overview

Every agent that produces code changes MUST self-critique before finalizing. This ensures code quality and surfaces issues before they become problems.

## When to Critique

- After writing ANY code (new functions, bug fixes, refactors)
- Before reporting COMPLETE status
- Before handing off to another agent

## Line-by-Line Review Checklist

For each significant line or block added, ask:

### Purpose
- [ ] Does this line serve a clear, necessary purpose?
- [ ] Could this be removed without breaking functionality?
- [ ] Is the intent clear to someone reading it for the first time?

### Simplicity
- [ ] Is there a simpler way to achieve this?
- [ ] Am I over-engineering this?
- [ ] Would a built-in function/library handle this better?

### Correctness
- [ ] What assumptions does this line make?
- [ ] What inputs would break this?
- [ ] Are edge cases handled (null, empty, negative, overflow)?

### Abstraction
- [ ] Is this the right abstraction level?
- [ ] Should this be extracted into a separate function?
- [ ] Is this too generic or too specific?

### Safety
- [ ] Could this cause a security vulnerability?
- [ ] Is user input properly validated/sanitized?
- [ ] Are errors handled appropriately?

## What to Document

Your self-critique output MUST include:

### 1. Line-by-Line Review Table

```markdown
| Line/Block | Purpose | Critique | Fix Applied |
|------------|---------|----------|-------------|
| `[code snippet]` | [why it exists] | [issue found or "Sound"] | [fix applied or "None"] |
```

### 2. Assumptions Made
List every assumption your code relies on:
- Data format assumptions
- Environment assumptions
- Caller behavior assumptions
- State assumptions

### 3. Edge Cases Not Covered
Be honest about what's NOT handled:
- Why it's not handled (out of scope, rare, acceptable risk)
- What would happen if that edge case occurred

### 4. Trade-offs Accepted
Document what you sacrificed for what gain:
- Readability vs performance
- Simplicity vs flexibility
- Speed vs thoroughness
- Memory vs CPU

## Example Good Critique

```markdown
## Self-Critique

| Code | Purpose | Critique | Fix Applied |
|------|---------|----------|-------------|
| `if (!user)` | Guard against null | Doesn't handle empty object `{}` | Changed to: `if (!user?.id)` |
| `users.filter(u => u.active)` | Get active users | Creates new array each call | Acceptable - called once per request |
| `return data.map(transform)` | Transform results | Assumes data is array | Added: `if (!Array.isArray(data)) return []` |
| `await Promise.all(tasks)` | Parallel execution | Fails fast on any rejection | Added: `Promise.allSettled` for resilience |

**Assumptions Made**:
- `user` object always has `id` property when valid
- `users` array fits in memory (no pagination needed)
- `transform` function is pure (no side effects)

**Edge Cases Not Covered**:
- Very large arrays (>10k items) - would need streaming
- Concurrent modifications to `users` - acceptable for read-only operation

**Trade-offs Accepted**:
- Chose `Promise.all` over sequential for speed, accepting fail-fast behavior
- Used in-memory filtering over database query for simplicity
```

## Example Bad Critique (Don't Do This)

```markdown
## Self-Critique

The code looks good. I checked everything and it should work.

**Assumptions**: None
**Edge Cases**: All handled
**Trade-offs**: None
```

Why it's bad:
- Too vague - doesn't examine specific code
- "Looks good" isn't a critique
- "None" is almost never true
- Doesn't help the reader understand the code's limits

## Critique Anti-Patterns

### Don't Be Vague
- BAD: "This might have issues"
- GOOD: "Line 15 assumes `data.items` exists - will throw if missing"

### Don't Over-Critique
- BAD: Nitpicking every style choice
- GOOD: Focus on correctness, security, and maintainability

### Don't Skip It
- BAD: "This is simple code, no critique needed"
- GOOD: Even simple code can have hidden assumptions

### Don't Be Defensive
- BAD: "This is the best possible approach"
- GOOD: "I chose X over Y because Z, but Y would work if [conditions]"

## Integration with Self-Reflection

Code critique is part of the larger self-reflection protocol (see `knowledge/self-reflection.md`):

1. **Task Alignment**: Does the code solve the actual problem?
2. **Code Critique**: Line-by-line review (this document)
3. **Confidence Assessment**: HIGH/MEDIUM/LOW based on critique findings
4. **Handoff Notes**: Key limitations for next agent

## Confidence Level Guidelines

Based on your critique findings:

| Finding | Confidence Impact |
|---------|-------------------|
| All assumptions verified | +HIGH |
| Unverified assumptions exist | MEDIUM max |
| Known unhandled edge cases | MEDIUM max |
| Significant trade-offs | Note in reasoning |
| Security concerns found | LOW until resolved |
| Blocking issues found | BLOCKED status |

## Quick Reference Checklist

Before marking code COMPLETE:

```
□ Reviewed each significant line/block
□ Documented all assumptions
□ Listed unhandled edge cases
□ Noted trade-offs made
□ Applied fixes for issues found
□ Updated confidence level based on findings
```

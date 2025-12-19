# Teaching Code Changes

TRIGGER: teach, explain, why, learn, understand, decision, approach, alternative, concept, pattern

## Core Principle

Every code change is a teaching opportunity. Explain **WHY**, not just **WHAT**.

Users should understand:
- Why you chose this approach
- What alternatives existed
- What concepts/patterns were applied
- What they should learn from this

## Teaching Output Structure

Every code change MUST include a Teaching section with:

### 1. Why This Approach
Explain the reasoning behind your implementation choice.

```markdown
**Why This Approach**:
[Context] We needed to [solve X] because [reason].
[Decision] I chose [approach] because [justification].
[Trade-off] This means [what we get] at the cost of [what we give up].
```

### 2. Alternatives Considered
Show you evaluated options - this teaches decision-making.

```markdown
**Alternatives Considered**:
| Alternative | Why Rejected |
|-------------|--------------|
| [Option A] | [Specific reason it wasn't chosen] |
| [Option B] | [Specific reason it wasn't chosen] |
```

### 3. Key Concepts Applied
Name and briefly explain the principles/patterns used.

```markdown
**Key Concepts Applied**:
1. **[Concept Name]**: [What it means and why it applies here]
2. **[Pattern Name]**: [Brief explanation of the pattern]
```

### 4. What You Should Learn
Extract transferable insights.

```markdown
**What You Should Learn**:
- [Generalizable insight 1]
- [Generalizable insight 2]
- [When to apply this pattern]
```

### 5. Questions to Deepen Understanding
Socratic questions that encourage thinking.

```markdown
**Questions to Think About**:
- What would happen if [edge case]?
- Why might you NOT want to [alternative approach]?
- How would this change if [different requirement]?
```

## Teaching by Code Type

### Bug Fixes

Focus on: Root cause understanding, prevention

```markdown
## Teaching

**Why This Fix**:
The bug occurred because [root cause]. The original code assumed [assumption]
which failed when [condition]. We fixed it by [approach] because this
addresses the root cause rather than just the symptom.

**What Caused This Bug**:
1. [Contributing factor 1]
2. [Contributing factor 2]

**How to Prevent Similar Bugs**:
- [Prevention strategy 1]
- [Prevention strategy 2]

**Key Concept: [Relevant Principle]**
[Explanation of defensive programming, fail-fast, etc.]
```

### Refactoring

Focus on: Code quality principles, when to apply

```markdown
## Teaching

**Why This Refactoring**:
The original code had [code smell] which causes [problem]. We applied
[refactoring technique] to achieve [benefit].

**Refactoring Technique: [Name]**
[Explanation of the technique from Martin Fowler, etc.]

**When to Apply This**:
- [Trigger condition 1]
- [Trigger condition 2]

**When NOT to Apply This**:
- [Counter-indication 1]
```

### New Features

Focus on: Design decisions, architecture patterns

```markdown
## Teaching

**Why This Design**:
Given the requirements [X, Y, Z], I chose [pattern/architecture] because
it provides [benefits] while allowing [flexibility].

**Design Pattern: [Name]**
[What it is, when to use it]

**Architecture Decision**:
- Chose [approach] over [alternative] because [reason]
- This enables [future capability] while [current benefit]

**SOLID Principles Applied**:
- **[Principle]**: How it applies here
```

### Tests

Focus on: Testing strategy, coverage philosophy

```markdown
## Teaching

**Why These Tests**:
I wrote [test types] because [reasoning]. The test strategy focuses on
[boundary conditions / happy paths / error cases] because [priority].

**Testing Concepts Applied**:
1. **[Concept]**: [How it applies - AAA pattern, mocking strategy, etc.]

**What's NOT Tested and Why**:
- [Untested scenario]: [Reason - integration test, out of scope, etc.]
```

## Teaching Techniques

### Progressive Disclosure

Start with the key insight, add details as needed:

```markdown
**The Key Insight**: [One sentence summary]

**Going Deeper**: [More detailed explanation]

**For the Curious**: [Advanced details or edge cases]
```

### Concrete Before Abstract

Show the specific example first, then the general principle:

```markdown
**What We Did**:
```javascript
// We used dependency injection here
constructor(private db: Database) {}
```

**The Pattern**:
Dependency Injection means passing dependencies in rather than creating them.
This makes the code testable (we can pass a mock) and flexible (we can swap implementations).
```

### Connect to Familiar Concepts

Reference things the user likely knows:

```markdown
**If You Know React**: This is like lifting state up - we moved the logic
to a parent so children can share it.

**If You Know SQL**: This is similar to a JOIN - we're combining data from
two sources based on a common key.
```

## Socratic Questions by Topic

### For Design Decisions
- "What would happen if we had 1000x more users?"
- "How would this change if requirement X was added?"
- "Why did we put this logic here instead of [alternative location]?"

### For Bug Fixes
- "What assumption did the original code make?"
- "How could we have caught this earlier?"
- "What test would have prevented this?"

### For Refactoring
- "What was the code smell that triggered this refactoring?"
- "When would you NOT apply this refactoring?"
- "How do you know when to stop refactoring?"

### For Performance
- "What's the time/space complexity of this approach?"
- "When would the simpler approach be good enough?"
- "How would you measure if this optimization helped?"

## Anti-Patterns in Teaching

### Don't Just State Facts
- BAD: "We used a factory pattern."
- GOOD: "We used a factory pattern because object creation involves conditional logic that would clutter the calling code."

### Don't Assume Knowledge
- BAD: "This follows SOLID principles."
- GOOD: "This follows the Single Responsibility Principle - each class has one job. The UserValidator only validates, it doesn't also save to the database."

### Don't Be Condescending
- BAD: "As any developer knows..."
- GOOD: "A common pattern here is..."

### Don't Over-Teach
- BAD: Explaining every basic operation
- GOOD: Focus on decisions, trade-offs, and non-obvious choices

## Integration with Self-Critique

Teaching and critique work together:

1. **Critique** reveals what could be better
2. **Teaching** explains why you made the choices you did

```markdown
## Self-Critique
[Found issue with edge case handling]

## Teaching
**Why We Didn't Handle [Edge Case]**:
We chose not to handle [edge case] because [reason]. If this becomes
a requirement, you would [approach to add it].
```

## Quick Reference Template

```markdown
## Teaching

**Why This Approach**:
[1-2 sentences on the reasoning]

**Alternatives Considered**:
| Alternative | Why Rejected |
|-------------|--------------|
| [A] | [Reason] |

**Key Concepts**:
1. **[Concept]**: [Brief explanation]

**What You Should Learn**:
- [Key insight]

**Questions**:
- [Thought-provoking question]
```

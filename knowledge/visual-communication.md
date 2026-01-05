# Visual Communication Knowledge Base

TRIGGER: explain, diagram, visual, architecture, show me, how does, what does, structure, flow, why

## Overview

This knowledge base provides templates and guidelines for visual ASCII diagram communication. The orchestrator uses these patterns as the DEFAULT communication mode for ALL explanations (RULE-021).

---

## Core Principle Frameworks

### SOLID Principles Quick Reference

| Principle | One-Line Description | Annotation Format |
|-----------|---------------------|-------------------|
| **SRP** | One class, one reason to change | `SRP: [responsibility]` |
| **OCP** | Extend without modifying | `OCP: [extension point]` |
| **LSP** | Subtypes replace base types | `LSP: [substitution]` |
| **ISP** | Small, focused interfaces | `ISP: [interface scope]` |
| **DIP** | Depend on abstractions | `DIP: [abstraction name]` |

### Gang of Four Patterns Quick Reference

| Category | Pattern | Use When | Annotation |
|----------|---------|----------|------------|
| **Creational** | Factory Method | Object creation varies | `GoF: Factory Method` |
| | Abstract Factory | Families of related objects | `GoF: Abstract Factory` |
| | Builder | Complex object construction | `GoF: Builder` |
| | Prototype | Clone existing objects | `GoF: Prototype` |
| | Singleton | Exactly one instance | `GoF: Singleton` |
| **Structural** | Adapter | Interface incompatibility | `GoF: Adapter` |
| | Bridge | Separate abstraction/impl | `GoF: Bridge` |
| | Composite | Tree structures | `GoF: Composite` |
| | Decorator | Add behavior dynamically | `GoF: Decorator` |
| | Facade | Simplify subsystem | `GoF: Facade` |
| | Flyweight | Share common state | `GoF: Flyweight` |
| | Proxy | Control access | `GoF: Proxy` |
| **Behavioral** | Chain of Responsibility | Pass request along chain | `GoF: Chain of Resp` |
| | Command | Encapsulate request | `GoF: Command` |
| | Iterator | Traverse collection | `GoF: Iterator` |
| | Mediator | Centralize communication | `GoF: Mediator` |
| | Memento | Capture/restore state | `GoF: Memento` |
| | Observer | Notify subscribers | `GoF: Observer` |
| | State | Behavior varies by state | `GoF: State` |
| | Strategy | Swap algorithms | `GoF: Strategy` |
| | Template Method | Define algorithm skeleton | `GoF: Template Method` |
| | Visitor | Add operations externally | `GoF: Visitor` |

### Clean Architecture Layers

```
+-------------------------------------------+
|           Frameworks & Drivers             |  ← Outermost: DB, Web, UI
|  +-------------------------------------+  |
|  |         Interface Adapters          |  |  ← Controllers, Presenters, Gateways
|  |  +-------------------------------+  |  |
|  |  |          Use Cases            |  |  |  ← Application business rules
|  |  |  +-------------------------+  |  |  |
|  |  |  |       Entities          |  |  |  |  ← Enterprise business rules
|  |  |  |     (innermost)         |  |  |  |
|  |  |  +-------------------------+  |  |  |
|  |  +-------------------------------+  |  |
|  +-------------------------------------+  |
+-------------------------------------------+

CA RULE: Dependencies ONLY point inward (toward Entities)
```

### Domain-Driven Design Concepts

| Concept | Description | Annotation |
|---------|-------------|------------|
| **Entity** | Has unique identity | `DDD: Entity` |
| **Value Object** | Defined by attributes, immutable | `DDD: Value Object` |
| **Aggregate** | Consistency boundary | `DDD: Aggregate` |
| **Aggregate Root** | Entry point for aggregate | `DDD: Aggregate Root` |
| **Bounded Context** | Model boundary, ubiquitous language | `DDD: Bounded Context` |
| **Domain Event** | Something significant happened | `DDD: Domain Event` |
| **Repository** | Collection-like interface for aggregates | `DDD: Repository` |
| **Service** | Stateless domain operation | `DDD: Domain Service` |

### CIA Triad (Security)

| Aspect | Meaning | Questions to Answer |
|--------|---------|---------------------|
| **Confidentiality** | Data protected from unauthorized access | Who can see this? Is it encrypted? Are access logs kept? |
| **Integrity** | Data accurate and unaltered | Can this be tampered with? Is it signed/validated? Are changes tracked? |
| **Availability** | System accessible when needed | What if this fails? Is there redundancy? What's the recovery time? |

### Object-Oriented Programming (OOP) - Four Pillars

```
+================================================================+
|                    OOP FOUR PILLARS                             |
+================================================================+
|                                                                 |
|  +------------------+           +------------------+            |
|  |  ENCAPSULATION   |           |   ABSTRACTION    |            |
|  +------------------+           +------------------+            |
|  | Bundle data +    |           | Hide complexity  |            |
|  | methods that     |           | Show only        |            |
|  | operate on it    |           | essential        |            |
|  | Hide internals   |           | features         |            |
|  +------------------+           +------------------+            |
|                                                                 |
|  +------------------+           +------------------+            |
|  |   INHERITANCE    |           |  POLYMORPHISM    |            |
|  +------------------+           +------------------+            |
|  | Create new class |           | Same interface   |            |
|  | from existing    |           | different        |            |
|  | Reuse code       |           | implementations  |            |
|  | IS-A relationship|           | Runtime behavior |            |
|  +------------------+           +------------------+            |
|                                                                 |
+================================================================+
```

| Pillar | One-Line Description | Annotation Format |
|--------|---------------------|-------------------|
| **Encapsulation** | Bundle data with methods, hide internals | `OOP: Encapsulated [what]` |
| **Abstraction** | Hide complexity, show essential features | `OOP: Abstracted [what]` |
| **Inheritance** | Create subclass from parent (IS-A) | `OOP: Inherits [parent]` |
| **Polymorphism** | Same interface, different behavior | `OOP: Polymorphic [interface]` |

### Test-Driven Development (TDD) - Red-Green-Refactor

```
+================================================================+
|                   TDD: RED-GREEN-REFACTOR                       |
+================================================================+
|                                                                 |
|   +------------------+                                          |
|   |    1. RED        |  Write a failing test first              |
|   |    (Write Test)  |  Test defines expected behavior          |
|   +------------------+                                          |
|            │                                                    |
|            ▼                                                    |
|   +------------------+                                          |
|   |    2. GREEN      |  Write minimum code to pass              |
|   |    (Make Pass)   |  Don't optimize yet                      |
|   +------------------+                                          |
|            │                                                    |
|            ▼                                                    |
|   +------------------+                                          |
|   |    3. REFACTOR   |  Clean up code                           |
|   |    (Improve)     |  Tests still pass                        |
|   +------------------+                                          |
|            │                                                    |
|            └──────────────────────────────────┐                 |
|                                               │                 |
|            ┌──────────────────────────────────┘                 |
|            │                                                    |
|            ▼                                                    |
|   +------------------+                                          |
|   |    REPEAT        |  Next test case                          |
|   +------------------+                                          |
|                                                                 |
+================================================================+
```

| Phase | Description | Annotation Format |
|-------|-------------|-------------------|
| **Red** | Write failing test first | `TDD: Test written for [behavior]` |
| **Green** | Minimum code to pass | `TDD: Passes [test name]` |
| **Refactor** | Improve without breaking | `TDD: Refactored [what]` |

### TDD Test Types Pyramid

```
          /\
         /  \         E2E Tests (Few)
        /    \        - Full system
       /──────\       - Slow, brittle
      /        \
     /  Integ   \     Integration Tests (Some)
    /   Tests    \    - Component interactions
   /──────────────\   - Medium speed
  /                \
 /    Unit Tests    \ Unit Tests (Many)
/____________________\- Single units
                      - Fast, isolated
                      - Most coverage
```

### Design Principles: KISS, DRY, YAGNI

| Principle | Meaning | Annotation | Anti-Pattern |
|-----------|---------|------------|--------------|
| **KISS** | Keep It Simple, Stupid | `KISS: Simplified [how]` | Over-engineering |
| **DRY** | Don't Repeat Yourself | `DRY: Extracted [what]` | Copy-paste code |
| **YAGNI** | You Aren't Gonna Need It | `YAGNI: Removed [what]` | Speculative features |

```
+================================================================+
|                  DESIGN PRINCIPLES                              |
+================================================================+
|                                                                 |
|  KISS: Simplest solution that works                             |
|  ├── Avoid unnecessary complexity                               |
|  ├── Prefer readable over clever                                |
|  └── Simple is easier to maintain                               |
|                                                                 |
|  DRY: Single source of truth                                    |
|  ├── Extract common logic to one place                          |
|  ├── Changes happen in one location                             |
|  └── BUT: Don't over-abstract prematurely                       |
|                                                                 |
|  YAGNI: Build only what's needed NOW                            |
|  ├── Delete unused code                                         |
|  ├── Don't build for hypothetical futures                       |
|  └── Easier to add later than remove                            |
|                                                                 |
+================================================================+
```

### GRASP Patterns (General Responsibility Assignment)

| Pattern | Description | Annotation Format |
|---------|-------------|-------------------|
| **Information Expert** | Assign responsibility to class with needed info | `GRASP: Expert [class]` |
| **Creator** | Who creates instances? Class that has initializing data | `GRASP: Creator [class]` |
| **Controller** | First object receiving UI events | `GRASP: Controller [class]` |
| **Low Coupling** | Minimize dependencies between classes | `GRASP: Low coupling via [how]` |
| **High Cohesion** | Focused, related responsibilities | `GRASP: Cohesive [class]` |
| **Polymorphism** | Use polymorphism for varying behavior | `GRASP: Polymorphic [interface]` |
| **Pure Fabrication** | Artificial class for cohesion/coupling | `GRASP: Fabrication [class]` |
| **Indirection** | Intermediate object to decouple | `GRASP: Indirection via [what]` |
| **Protected Variations** | Wrap instability points | `GRASP: Protected [variation]` |

### Clean Code Principles

```
+================================================================+
|                    CLEAN CODE PRINCIPLES                        |
+================================================================+
|                                                                 |
|  NAMING:                                                        |
|  - Use intention-revealing names                                |
|  - Avoid abbreviations and single letters                       |
|  - Classes = nouns, Methods = verbs                             |
|                                                                 |
|  FUNCTIONS:                                                     |
|  - Small (< 20 lines ideal)                                     |
|  - Do one thing (SRP for functions)                             |
|  - Few parameters (0-3 ideal)                                   |
|  - No side effects                                              |
|                                                                 |
|  COMMENTS:                                                      |
|  - Code should be self-documenting                              |
|  - Comments explain WHY, not WHAT                               |
|  - Bad comments = code smell                                    |
|                                                                 |
|  ERROR HANDLING:                                                |
|  - Use exceptions, not return codes                             |
|  - Don't return null                                            |
|  - Fail fast                                                    |
|                                                                 |
+================================================================+
```

| Principle | Annotation Format | Example |
|-----------|-------------------|---------|
| Meaningful Names | `Clean: Named [intent]` | `getUserById` not `get` |
| Small Functions | `Clean: Small [lines]` | `< 20 lines` |
| Single Purpose | `Clean: Single purpose [what]` | One thing well |
| No Side Effects | `Clean: Pure function` | Input → output only |

### Hexagonal Architecture (Ports & Adapters)

```
+================================================================+
|                  HEXAGONAL ARCHITECTURE                         |
|                   (Ports & Adapters)                            |
+================================================================+
|                                                                 |
|        [Web UI]     [CLI]     [API Client]                      |
|            \          |          /                              |
|             \         |         /                               |
|              v        v        v                                |
|         +─────────────────────────────+                         |
|         |     PRIMARY ADAPTERS        |  ← Driving adapters     |
|         |   (Controllers, Handlers)   |                         |
|         +─────────────────────────────+                         |
|                      │                                          |
|                      v                                          |
|         +─────────────────────────────+                         |
|         |      PRIMARY PORTS          |  ← Input interfaces     |
|         |    (Use Case Interfaces)    |                         |
|         +─────────────────────────────+                         |
|                      │                                          |
|                      v                                          |
|         +─────────────────────────────+                         |
|         |       APPLICATION           |                         |
|         |       CORE/DOMAIN           |  ← Business logic       |
|         +─────────────────────────────+                         |
|                      │                                          |
|                      v                                          |
|         +─────────────────────────────+                         |
|         |     SECONDARY PORTS         |  ← Output interfaces    |
|         |   (Repository Interfaces)   |                         |
|         +─────────────────────────────+                         |
|                      │                                          |
|                      v                                          |
|         +─────────────────────────────+                         |
|         |    SECONDARY ADAPTERS       |  ← Driven adapters      |
|         |  (DB, External Services)    |                         |
|         +─────────────────────────────+                         |
|                      │                                          |
|                      v                                          |
|        [Database]  [Email]  [Payment Gateway]                   |
|                                                                 |
+================================================================+
| Key: Domain is ISOLATED from all external concerns              |
|      Dependencies point INWARD toward domain                    |
+================================================================+
```

### CQRS (Command Query Responsibility Segregation)

```
+================================================================+
|                         CQRS                                    |
+================================================================+
|                                                                 |
|   [Client Request]                                              |
|          │                                                      |
|          ├─────────────────┬─────────────────┐                 |
|          │                 │                 │                  |
|          ▼                 │                 ▼                  |
|   +-------------+          │          +-------------+           |
|   |   COMMAND   |          │          |    QUERY    |           |
|   |   (Write)   |          │          |   (Read)    |           |
|   +-------------+          │          +-------------+           |
|          │                 │                 │                  |
|          ▼                 │                 ▼                  |
|   +-------------+          │          +-------------+           |
|   | Write Model |          │          | Read Model  |           |
|   | (Normalized)|          │          |(Denormalized|           |
|   +-------------+          │          +-------------+           |
|          │                 │                 │                  |
|          │     [Event]     │                 │                  |
|          └────────────────►│◄────────────────┘                  |
|                            │                                    |
|                     [Sync/Async]                                |
|                                                                 |
+================================================================+
| Commands: Change state, return void/ID                          |
| Queries: Return data, no side effects                           |
+================================================================+
```

### Event Sourcing

```
+================================================================+
|                     EVENT SOURCING                              |
+================================================================+
|                                                                 |
|  Traditional:              Event Sourced:                       |
|  +-----------+             +-----------+                        |
|  | Current   |             | Event 1   | → OrderCreated         |
|  | State     |             | Event 2   | → ItemAdded            |
|  | (Latest)  |             | Event 3   | → ItemAdded            |
|  +-----------+             | Event 4   | → ItemRemoved          |
|                            | Event 5   | → OrderPaid            |
|                            +-----------+                        |
|                                  │                              |
|                                  ▼                              |
|                            +-----------+                        |
|                            | Replay    | → Current State        |
|                            +-----------+                        |
|                                                                 |
+================================================================+
| Benefits: Full audit trail, time travel, event replay           |
| Challenges: Eventual consistency, event schema evolution        |
+================================================================+
```

---

## Diagram Templates

### Template 1: Layered Architecture Box

Use for: Component relationships, system structure, module dependencies

```
+================================================================+
|                         LAYER NAME                              |
|                    (Layer Type: Domain/App/Infra)               |
+================================================================+
|                                                                 |
|  +---------------------------+  +---------------------------+   |
|  |      Component A          |  |      Component B          |   |
|  |  SRP: [responsibility]    |  |  SRP: [responsibility]    |   |
|  +---------------------------+  +---------------------------+   |
|             |                              |                    |
|             +------------+  +--------------+                    |
|                          |  |                                   |
|                          v  v                                   |
|                   +-------------+                               |
|                   | Interface   |  ← DIP: Abstraction           |
|                   +-------------+                               |
|                                                                 |
+================================================================+
| CA: [Clean Architecture layer rule]                             |
| GoF: [Patterns used]                                            |
+================================================================+
```

### Template 2: Request Flow / Decision Tree

Use for: Logic explanation, conditional behavior, process flows

```
[Input/Request]
       │
       ▼
+------------------+
|   ENTRY POINT    |  ← CA: Interface Adapter
+------------------+
       │
       ▼
+------------------+
|   DECISION       |
+------------------+
       │
       ├── YES ──────────────────┐
       │                         │
       │                         ▼
       │                 +------------------+
       │                 |   Action A       |  ← OCP: Extended via [X]
       │                 +------------------+
       │
       └── NO ───────────────────┐
                                 │
                                 ▼
                         +------------------+
                         |   Action B       |  ← GoF: [Pattern]
                         +------------------+
```

### Template 3: Component Interaction

Use for: Communication between parts, data flow, API interactions

```
+------------+     +------------+     +------------+
|  Client    | --> | Interface  | --> |   Impl     |
|            |     | (Port)     |     | (Adapter)  |
+------------+     +------------+     +------------+
                         ^
                         │
                   DIP: Depends on abstraction
                   CA: Port separates domain from infrastructure
```

### Template 4: Security Context Box

Use for: Any security-relevant explanation (ALWAYS include for security topics)

```
+================================================================+
|                      SECURITY DECISION                          |
+================================================================+
| CIA Impact:                                                     |
|   - Confidentiality: [How this protects/exposes data]           |
|   - Integrity: [How this ensures data accuracy]                 |
|   - Availability: [How this affects system access]              |
+================================================================+
| OWASP Relevance: [A01-A10 if applicable]                        |
| Threat Model: [What threats does this address?]                 |
+================================================================+
```

### Template 5: Comparison Matrix

Use for: Evaluating alternatives, showing trade-offs

```
+==================+============+============+============+
|     Criterion    |  Option A  |  Option B  |  Option C  |
+==================+============+============+============+
| SOLID Compliance |    HIGH    |   MEDIUM   |    LOW     |
| Maintainability  |    HIGH    |    HIGH    |   MEDIUM   |
| Performance      |   MEDIUM   |    HIGH    |    HIGH    |
| Complexity       |    LOW     |   MEDIUM   |    HIGH    |
| Testability      |    HIGH    |   MEDIUM   |    LOW     |
+==================+============+============+============+
| Selected: Option [X]                                     |
| Rationale: [principle-based reason]                      |
+==========================================================+
```

### Template 6: Before/After Refactoring

Use for: Showing violations and fixes, code evolution

```
BEFORE (Violations)                AFTER (Compliant)
+-------------------+             +-------------------+
| GodClass          |             | ClassA            |
| - method1()       |    SRP      | - responsibility1 |
| - method2()       |   ====>     +-------------------+
| - method3()       |             | ClassB            |
| [SRP VIOLATION]   |             | - responsibility2 |
+-------------------+             +-------------------+
                                  | ClassC            |
                                  | - responsibility3 |
                                  +-------------------+

OCP: Now each can extend independently
DIP: Classes depend on interfaces, not each other
```

### Template 7: Dependency Injection Flow

Use for: Explaining how DI wires components together

```
+-------------------------------------------+
|           DI Container (Startup)           |
+-------------------------------------------+
|                                           |
|  Register:                                |
|  ┌─────────────────────────────────────┐  |
|  │ IService → ConcreteService          │  |
|  │ IRepository → SqlRepository         │  |
|  └─────────────────────────────────────┘  |
|                                           |
+-------------------------------------------+
              │
              │ At Runtime
              ▼
+-------------------------------------------+
|           Consumer Created                 |
+-------------------------------------------+
|                                           |
|  new Controller(                          |
|      container.Resolve<IService>()        |
|  )                                        |
|                                           |
|  DIP: Controller depends on IService      |
|       (abstraction), not ConcreteService  |
|       (implementation)                    |
|                                           |
+-------------------------------------------+
```

---

## Response Complexity Guidelines

| Question Complexity | Diagram Size | Annotations Required | Example |
|--------------------|--------------|---------------------|---------|
| **Simple** | 1 small box | 1-2 principles | "What does X do?" |
| **Medium** | 2-3 connected boxes | 3-4 principles | "How does X work?" |
| **Complex** | Full layered diagram | 5+ principles | "Design for X" |
| **Security** | Include CIA box | CIA + OWASP ref | "Is this secure?" |

---

## Principle Annotation Requirements

When explaining code or architecture, ALWAYS annotate with relevant principles:

| Annotation | Format | Example |
|------------|--------|---------|
| SRP | `SRP: [reason]` | `SRP: Handles only user validation` |
| OCP | `OCP: [extension point]` | `OCP: New validators via interface` |
| LSP | `LSP: [substitution note]` | `LSP: All handlers interchangeable` |
| ISP | `ISP: [interface focus]` | `ISP: Read-only vs read-write split` |
| DIP | `DIP: [abstraction]` | `DIP: Depends on IRepository` |
| GoF | `GoF: [Pattern]` | `GoF: Strategy for algorithms` |
| DDD | `DDD: [concept]` | `DDD: Aggregate root boundary` |
| CA | `CA: [layer rule]` | `CA: Domain knows no frameworks` |
| CIA | `CIA: [aspect]` | `CIA: C - encrypted at rest` |

---

## Complete Example Response

**User Question**: "How does the order processing system work?"

**Orchestrator Response**:

```
+================================================================+
|                   ORDER PROCESSING SYSTEM                       |
|                    (Clean Architecture)                         |
+================================================================+
|                                                                 |
|   [Customer Request]                                            |
|          │                                                      |
|          ▼                                                      |
|   +---------------------------+                                 |
|   |    OrderController        |  ← CA: Interface Adapter        |
|   |    (HTTP concerns only)   |    SRP: HTTP request handling   |
|   +---------------------------+                                 |
|          │                                                      |
|          ▼                                                      |
|   +---------------------------+                                 |
|   |    PlaceOrderUseCase      |  ← CA: Application Layer        |
|   |    (orchestrates flow)    |    SRP: Order placement logic   |
|   +---------------------------+                                 |
|          │                                                      |
|          ├───────────────┬───────────────┐                     |
|          ▼               ▼               ▼                      |
|   +-------------+  +-------------+  +-------------+            |
|   | IOrderRepo  |  | IPaymentSvc |  | IInventory  |            |
|   | (Port)      |  | (Port)      |  | (Port)      |            |
|   +-------------+  +-------------+  +-------------+            |
|          │               │               │                      |
|   DIP: Use case depends on abstractions only                   |
|          │               │               │                      |
|          ▼               ▼               ▼                      |
|   +-------------+  +-------------+  +-------------+            |
|   | SqlOrderRepo|  | StripeGateway|  | WarehouseAPI|           |
|   | (Adapter)   |  | (Adapter)    |  | (Adapter)   |           |
|   +-------------+  +-------------+  +-------------+            |
|                                                                 |
+================================================================+
|  DOMAIN MODEL (DDD):                                            |
|  - Order: Aggregate Root (owns OrderLines, controls invariants) |
|  - OrderLine: Entity (has identity within Order)                |
|  - Money: Value Object (immutable, equality by value)           |
|  - OrderPlaced: Domain Event (triggers downstream processes)    |
+================================================================+
|  PATTERNS:                                                      |
|  - GoF: Strategy (swappable payment providers)                  |
|  - GoF: Repository (abstracts persistence)                      |
|  - GoF: Observer (domain events notify subscribers)             |
+================================================================+
|  SECURITY (CIA):                                                |
|  - C: Payment data never stored, tokenized via Stripe           |
|  - I: Order totals calculated server-side, signed               |
|  - A: Retry logic for payment gateway timeouts                  |
+================================================================+
```

---

## Integration with RULE-021

This knowledge base is referenced by RULE-021 (Visual Communication Standard).

When the orchestrator answers ANY question directly:
1. Identify question category (simple/medium/complex/security)
2. Select appropriate diagram template from this file
3. Add principle annotations
4. Include CIA box if security-relevant
5. Conclude with key insight summary

**MANDATORY**: Every explanation includes at least ONE framework annotation.

# Coding Standards & Best Practices

TRIGGER: SOLID, OOP, design pattern, Gang of Four, GoF, abstraction, clean code, code quality, coding standard, best practice, principle

## Overview

This document defines the coding standards ALL code-producing agents MUST validate against before reporting COMPLETE. Standards are organized by priority and include validation checklists.

## Priority Hierarchy

When standards conflict, follow this precedence:

```
1. Security      (never compromise)
2. Correctness   (code must work)
3. Maintainability (others must understand)
4. Performance   (within requirements)
5. Simplicity    (less is more)
```

---

## SOLID Principles

### S - Single Responsibility Principle (SRP)

**Definition**: A class should have only one reason to change.

**Validation Checklist**:
- [ ] Can you describe what this class does in one sentence without "and"?
- [ ] Does this class have only one actor (stakeholder) that would request changes?
- [ ] Are all methods related to the class's single purpose?
- [ ] Would changing one feature require changing unrelated methods?

**Violation Signs**:
- Class name contains "And" or "Manager" or "Handler" (too broad)
- Class has methods for unrelated concerns (e.g., `User` class with `sendEmail()`)
- Change in one feature breaks unrelated tests
- Class imports from many unrelated modules

**Fix Pattern**: Extract Class, Extract Interface

```python
# VIOLATION: User handles both data AND notifications
class User:
    def __init__(self, name, email):
        self.name = name
        self.email = email

    def save(self):
        db.save(self)

    def send_welcome_email(self):  # Wrong responsibility!
        email_service.send(self.email, "Welcome!")

# FIXED: Separate responsibilities
class User:
    def __init__(self, name, email):
        self.name = name
        self.email = email

    def save(self):
        db.save(self)

class UserNotificationService:
    def send_welcome_email(self, user):
        email_service.send(user.email, "Welcome!")
```

---

### O - Open/Closed Principle (OCP)

**Definition**: Software entities should be open for extension but closed for modification.

**Validation Checklist**:
- [ ] Can new behavior be added without modifying existing code?
- [ ] Are there extension points (interfaces, abstract classes, hooks)?
- [ ] Does adding a new case require changing switch/if-else chains?
- [ ] Is the code parameterized for variation?

**Violation Signs**:
- Adding new feature requires modifying existing switch statements
- Type-checking conditionals (`if isinstance(x, TypeA)`)
- Hardcoded behaviors that could vary
- No abstraction layer for varying behaviors

**Fix Pattern**: Strategy Pattern, Template Method, Plugin Architecture

```python
# VIOLATION: Must modify function for each new shape
def calculate_area(shape):
    if shape.type == "circle":
        return 3.14 * shape.radius ** 2
    elif shape.type == "rectangle":
        return shape.width * shape.height
    # Must add new elif for each shape!

# FIXED: Open for extension via polymorphism
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self) -> float:
        pass

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return 3.14 * self.radius ** 2

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height

# New shapes just implement Shape - no modification needed!
```

---

### L - Liskov Substitution Principle (LSP)

**Definition**: Subtypes must be substitutable for their base types without altering program correctness.

**Validation Checklist**:
- [ ] Can any subclass replace the parent without breaking behavior?
- [ ] Do subclasses honor all parent contracts (preconditions, postconditions)?
- [ ] Does subclass throw unexpected exceptions?
- [ ] Are return types consistent (covariant)?
- [ ] Are parameter types consistent (contravariant)?

**Violation Signs**:
- Subclass throws `NotImplementedError` for parent methods
- Subclass narrows acceptable inputs
- Subclass returns unexpected types
- Code checks `isinstance()` to handle subclass differently
- "Square extends Rectangle" problems

**Fix Pattern**: Extract Interface, Replace Inheritance with Composition

```python
# VIOLATION: Square can't properly substitute Rectangle
class Rectangle:
    def __init__(self, width, height):
        self._width = width
        self._height = height

    def set_width(self, width):
        self._width = width

    def set_height(self, height):
        self._height = height

    def area(self):
        return self._width * self._height

class Square(Rectangle):  # Violates LSP!
    def set_width(self, width):
        self._width = width
        self._height = width  # Unexpected side effect!

    def set_height(self, height):
        self._width = height
        self._height = height

# This breaks when substituting:
def resize(rect: Rectangle):
    rect.set_width(5)
    rect.set_height(10)
    assert rect.area() == 50  # Fails for Square!

# FIXED: Separate abstractions
class Shape(ABC):
    @abstractmethod
    def area(self) -> float:
        pass

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height

class Square(Shape):
    def __init__(self, side):
        self.side = side

    def area(self):
        return self.side ** 2
```

---

### I - Interface Segregation Principle (ISP)

**Definition**: Clients should not be forced to depend on interfaces they don't use.

**Validation Checklist**:
- [ ] Are interfaces small and focused?
- [ ] Do any implementations have empty/stub methods?
- [ ] Do clients use all methods of the interfaces they depend on?
- [ ] Could the interface be split into smaller, cohesive parts?

**Violation Signs**:
- Interface with 10+ methods
- Implementations with `pass` or `raise NotImplementedError`
- Classes implementing interfaces they only partially need
- "Fat interfaces" that serve multiple client types

**Fix Pattern**: Split Interface, Role Interface

```python
# VIOLATION: Fat interface forces unnecessary implementations
class Worker(ABC):
    @abstractmethod
    def work(self): pass

    @abstractmethod
    def eat(self): pass

    @abstractmethod
    def sleep(self): pass

class Robot(Worker):
    def work(self):
        print("Working...")

    def eat(self):
        pass  # Robots don't eat! Forced to implement.

    def sleep(self):
        pass  # Robots don't sleep!

# FIXED: Segregated interfaces
class Workable(ABC):
    @abstractmethod
    def work(self): pass

class Eatable(ABC):
    @abstractmethod
    def eat(self): pass

class Sleepable(ABC):
    @abstractmethod
    def sleep(self): pass

class Human(Workable, Eatable, Sleepable):
    def work(self): print("Working...")
    def eat(self): print("Eating...")
    def sleep(self): print("Sleeping...")

class Robot(Workable):  # Only implements what it needs
    def work(self): print("Working...")
```

---

### D - Dependency Inversion Principle (DIP)

**Definition**: High-level modules should not depend on low-level modules. Both should depend on abstractions.

**Validation Checklist**:
- [ ] Do classes depend on interfaces/abstractions, not concrete implementations?
- [ ] Are dependencies injected rather than instantiated internally?
- [ ] Can implementations be swapped without changing dependent code?
- [ ] Are imports from stable abstractions, not volatile concretions?

**Violation Signs**:
- `new` keyword inside business logic
- Direct imports of concrete implementations
- Hardcoded class instantiation
- Cannot mock dependencies for testing

**Fix Pattern**: Dependency Injection, Abstract Factory, Service Locator

```python
# VIOLATION: Direct dependency on concrete implementation
class OrderService:
    def __init__(self):
        self.db = MySQLDatabase()  # Hardcoded!
        self.mailer = SMTPMailer()  # Hardcoded!

    def place_order(self, order):
        self.db.save(order)
        self.mailer.send(order.customer_email, "Order placed!")

# FIXED: Depend on abstractions, inject dependencies
class Database(ABC):
    @abstractmethod
    def save(self, entity): pass

class Mailer(ABC):
    @abstractmethod
    def send(self, to, message): pass

class OrderService:
    def __init__(self, db: Database, mailer: Mailer):
        self.db = db
        self.mailer = mailer

    def place_order(self, order):
        self.db.save(order)
        self.mailer.send(order.customer_email, "Order placed!")

# Now can inject any implementation:
service = OrderService(
    db=PostgresDatabase(),
    mailer=SendGridMailer()
)
```

---

## Gang of Four Design Patterns

### When to Use Each Pattern

| Pattern | Use When | Don't Use When |
|---------|----------|----------------|
| **Factory Method** | Object creation varies by context | Creation logic is trivial |
| **Abstract Factory** | Family of related objects needed | Only one product type |
| **Builder** | Complex object with many optional parts | Object is simple |
| **Singleton** | Exactly one instance needed globally | State causes testing issues |
| **Adapter** | Interface incompatibility | Can modify the source |
| **Decorator** | Add behavior dynamically | Static composition works |
| **Facade** | Simplify complex subsystem | Already simple |
| **Proxy** | Control access to object | Direct access is fine |
| **Observer** | One-to-many notification | Tight coupling acceptable |
| **Strategy** | Interchangeable algorithms | Algorithm never varies |
| **Command** | Encapsulate requests as objects | Simple direct calls work |
| **Template Method** | Algorithm with variant steps | No invariant structure |
| **State** | Behavior changes with state | Few states, simple logic |

### Pattern Validation Checklist

Before using a design pattern:
- [ ] Is the pattern solving a real problem, not hypothetical?
- [ ] Is the complexity justified by the flexibility gained?
- [ ] Would a simpler solution work for current requirements?
- [ ] Does the team understand this pattern?
- [ ] Is the pattern correctly implemented (not cargo-culted)?

### Common Pattern Misapplications

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| Singleton abuse | Global state, testing issues | Use dependency injection |
| Factory for everything | Unnecessary indirection | Direct instantiation if simple |
| Over-decorated | Too many layers | Flatten if not needed |
| Observer spaghetti | Can't track who's listening | Use explicit event handling |
| Strategy overkill | One algorithm that never changes | Inline the logic |

---

## Code Quality Metrics

### Hard Limits (MUST enforce)

| Metric | Threshold | Action if Exceeded |
|--------|-----------|-------------------|
| Cyclomatic Complexity | ≤ 10 per method | Extract methods |
| Method Length | ≤ 40 lines | Extract methods |
| Class Length | ≤ 300 lines | Extract classes |
| Parameter Count | ≤ 4 parameters | Introduce Parameter Object |
| Nesting Depth | ≤ 3 levels | Extract, early return |
| Inheritance Depth | ≤ 3 levels | Favor composition |

### Soft Limits (SHOULD enforce)

| Metric | Target | Guidance |
|--------|--------|----------|
| Test Coverage | ≥ 80% | Focus on critical paths |
| Duplication | ≤ 3% | DRY, but don't over-abstract |
| Dependencies per Class | ≤ 5 | More suggests SRP violation |
| Public Methods per Class | ≤ 7 | More suggests ISP violation |

### Complexity Calculation

**Cyclomatic Complexity** = 1 + number of decision points

Decision points:
- `if`, `elif`, `else if`
- `while`, `for`, `foreach`
- `case` in switch
- `catch`, `except`
- `&&`, `||`, `and`, `or`
- `?:` ternary operator

```python
# Complexity = 1 + 4 = 5 (acceptable)
def process(items):
    result = []
    for item in items:           # +1
        if item.is_valid():      # +1
            if item.priority:    # +1
                result.insert(0, item)
            else:
                result.append(item)
        elif item.is_fixable():  # +1
            result.append(item.fix())
    return result
```

---

## OOP Best Practices

### Composition Over Inheritance

**Prefer composition when**:
- Relationship is "has-a" not "is-a"
- Behavior needs to change at runtime
- Multiple behaviors need to be combined
- Inheritance hierarchy would be deep

**Use inheritance when**:
- True "is-a" relationship exists
- Behavior is truly shared and stable
- Framework requires it

```python
# PREFER: Composition
class Car:
    def __init__(self, engine: Engine, transmission: Transmission):
        self.engine = engine
        self.transmission = transmission

# AVOID: Deep inheritance
class Vehicle:
    pass

class Car(Vehicle):
    pass

class SportsCar(Car):
    pass

class ElectricSportsCar(SportsCar):  # Too deep!
    pass
```

### Encapsulation Rules

1. **Make fields private by default**
2. **Expose only what's necessary**
3. **Use getters/setters for controlled access**
4. **Never expose internal collections directly**
5. **Immutable by default where possible**

```python
# BAD: Leaking internal state
class Order:
    def __init__(self):
        self.items = []  # Direct access allows modification!

# GOOD: Encapsulated
class Order:
    def __init__(self):
        self._items = []

    @property
    def items(self):
        return tuple(self._items)  # Immutable copy

    def add_item(self, item):
        self._items.append(item)
```

### Cohesion Guidelines

**High cohesion indicators**:
- All methods use most instance variables
- Methods relate to single concept
- Class name clearly describes purpose
- Easy to test in isolation

**Low cohesion indicators**:
- Methods fall into unrelated groups
- Some methods don't use instance state
- Class has multiple responsibilities
- Hard to name the class

---

## Abstraction Guidelines

### Right Level of Abstraction

| Too Abstract | Just Right | Too Concrete |
|--------------|------------|--------------|
| `DataProcessor` | `InvoiceValidator` | `InvoiceFieldLengthChecker` |
| `Manager` | `OrderService` | `OrderDatabaseSaverAndEmailer` |
| `Handler` | `PaymentGateway` | `StripePaymentWithRetryAndLogging` |

### When to Abstract

**Do abstract when**:
- Multiple implementations exist or likely
- Implementation details should be hidden
- Code needs to be testable in isolation
- Feature will have variations

**Don't abstract when**:
- Only one implementation ever
- Abstraction adds complexity without benefit
- YAGNI applies
- Abstraction leaks implementation details anyway

### Leaky Abstraction Signs

- Clients need implementation knowledge to use correctly
- Exception types reveal implementation (e.g., `SQLException` from repository)
- Configuration requires implementation-specific settings
- Performance characteristics vary unexpectedly

---

## Standards Validation Template

**Agents MUST verify this before reporting COMPLETE:**

```markdown
## Standards Compliance Check

### SOLID Principles
- [ ] **SRP**: Each class has single responsibility
- [ ] **OCP**: Design supports extension without modification
- [ ] **LSP**: Subtypes are substitutable for base types
- [ ] **ISP**: Interfaces are small and focused
- [ ] **DIP**: Dependencies point to abstractions

### Code Metrics
- [ ] Cyclomatic complexity ≤ 10 per method
- [ ] Method length ≤ 40 lines
- [ ] Class length ≤ 300 lines
- [ ] Parameter count ≤ 4
- [ ] Nesting depth ≤ 3

### Design Patterns (if applicable)
- [ ] Pattern choice is justified
- [ ] Pattern is correctly implemented
- [ ] No anti-patterns present

### OOP Best Practices
- [ ] Composition preferred over deep inheritance
- [ ] Encapsulation maintained
- [ ] High cohesion within classes
- [ ] Low coupling between classes

### Violations Found
| Principle | Location | Issue | Severity |
|-----------|----------|-------|----------|
| [SRP/OCP/...] | [file:line] | [description] | [High/Med/Low] |

### Fixes Applied
[Describe how violations were addressed]
```

---

## Quick Reference Card

### SOLID in One Line Each

- **S**ingle Responsibility: One class, one reason to change
- **O**pen/Closed: Extend behavior without modifying code
- **L**iskov Substitution: Subclasses work where parents work
- **I**nterface Segregation: Small, focused interfaces
- **D**ependency Inversion: Depend on abstractions, not concretions

### Code Smell → Fix Mapping

| Smell | Fix |
|-------|-----|
| Long Method | Extract Method |
| Large Class | Extract Class |
| Long Parameter List | Introduce Parameter Object |
| Switch Statements | Replace with Polymorphism |
| Duplicate Code | Extract Method/Class |
| Feature Envy | Move Method |
| Data Clumps | Extract Class |
| Primitive Obsession | Replace with Value Object |
| Inappropriate Intimacy | Move Method, Extract Class |
| Message Chains | Hide Delegate |

### Pattern Selection Shortcuts

- Need one instance? → Singleton (but consider DI first)
- Creating objects varies? → Factory Method
- Family of objects? → Abstract Factory
- Complex construction? → Builder
- Incompatible interface? → Adapter
- Add behavior dynamically? → Decorator
- Simplify subsystem? → Facade
- Control access? → Proxy
- Notify many objects? → Observer
- Swap algorithms? → Strategy
- Vary algorithm steps? → Template Method
- Behavior depends on state? → State

---

*This document is referenced by all code-producing agents per RULE-017.*

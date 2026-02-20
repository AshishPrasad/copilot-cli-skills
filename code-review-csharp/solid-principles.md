# SOLID Design Principles

### Single Responsibility Principle (SRP)
- Each class should have only one reason to change. Flag classes that mix unrelated concerns (e.g., a class that handles both HTTP requests and database access).
- Watch for "God classes" with many dependencies or methods spanning multiple domains.
- Prefer splitting large classes into focused, single-purpose components.

### Open/Closed Principle (OCP)
- Classes should be open for extension but closed for modification. Prefer adding new behavior through inheritance, interfaces, or composition rather than modifying existing code.
- Flag `switch`/`if-else` chains on type discriminators that would need modification each time a new variant is added — prefer polymorphism or the strategy pattern.
- Use abstractions (`interface`, `abstract class`) to allow extending behavior without changing existing implementations.

### Liskov Substitution Principle (LSP)
- Derived classes must be substitutable for their base classes without altering program correctness.
- Flag overrides that throw `NotSupportedException` or `NotImplementedException` for base class methods — this violates LSP.
- Ensure derived classes honor the contracts (preconditions, postconditions, invariants) established by their base types.
- Watch for base class methods that check `is` or `GetType()` to change behavior based on the derived type.

### Interface Segregation Principle (ISP)
- Prefer small, focused interfaces over large "fat" interfaces. Clients should not be forced to depend on methods they do not use.
- Flag interfaces with many members where most implementors leave methods as `throw new NotImplementedException()`.
- Split large interfaces into role-specific ones (e.g., `IReadableRepository` and `IWritableRepository` instead of a single `IRepository` with both read and write methods).

### Dependency Inversion Principle (DIP)
- High-level modules should not depend on low-level modules — both should depend on abstractions.
- Flag direct instantiation of dependencies (`new`) inside business logic classes. Prefer constructor injection of interfaces.
- Ensure service registrations in DI containers use interface-based bindings, not concrete class bindings.
- Watch for static helper classes or singletons used in place of injected dependencies, as they hinder testability.

# Modern C# & .NET Idioms

- Prefer **primary constructors** (C# 12+) for simple DI and DTOs.
- Use **records** for immutable data transfer objects (also gives correct `Equals`, `GetHashCode`, and `ToString` for free).
- Use **record structs** (C# 10+) for value types that need proper equality and hashing without manual implementation.
- Prefer **pattern matching** (`is`, `switch` expressions) over type-casting chains.
- Use **file-scoped namespaces** to reduce nesting.
- Use **raw string literals** (`"""..."""`) for multi-line strings.
- Prefer **target-typed `new()`** when the type is evident from context.
- Use `required` modifier for properties that must be set at initialization (C# 11+).

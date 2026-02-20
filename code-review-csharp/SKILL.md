---
name: code-review-csharp
description: Reviews C# code for .NET best practices, performance, security, and maintainability. Use this skill when asked to review C# or .NET code, or when performing code reviews on .cs files. Incorporates rules from ErrorProne.NET analyzers.
---

# C# / .NET Code Review

When reviewing C# code, analyze the code systematically against the following categories and report only **genuine issues** — bugs, security vulnerabilities, performance problems, and maintainability concerns. Do not comment on style preferences or trivial formatting.

This skill incorporates patterns from [ErrorProne.NET](https://github.com/SergeyTeplyakov/ErrorProne.NET) Roslyn analyzers (EPC/ERP rules) alongside general .NET best practices.

## Review Process

1. **Identify the scope**: Determine which `.cs` files are being reviewed. If reviewing a diff or PR, focus on changed lines and their immediate context.
2. **Categorize findings** by severity:
   - 🔴 **Critical** — Bugs, security vulnerabilities, data loss risks
   - 🟠 **Warning** — Performance issues, potential runtime failures, misuse of APIs
   - 🟡 **Suggestion** — Maintainability improvements, idiomatic .NET patterns
3. **Provide actionable feedback**: For each finding, explain *why* it is a problem, reference the relevant rule ID when applicable (e.g., `EPC31`), and suggest a concrete fix with a code snippet.

## .NET Best Practices Checklist

### Naming & Conventions
- Public members use **PascalCase**; private fields use **_camelCase** with underscore prefix.
- Async methods are suffixed with `Async`.
- Interfaces are prefixed with `I`.
- Boolean properties/methods use `Is`, `Has`, `Can`, or `Should` prefixes where appropriate.

### Null Safety & Defensive Coding
- Prefer **nullable reference types** (`#nullable enable`) and avoid `null` where possible.
- Guard public API parameters with `ArgumentNullException.ThrowIfNull()` (.NET 6+) or null checks.
- Avoid `null` return values from methods; prefer empty collections, `Option<T>`, or `default`.
- Flag unguarded dereferences of nullable types.

### Async / Await
- **[EPC27]** Never use `async void` except for event handlers; prefer `async Task`.
- **[EPC17]** Avoid `async void` delegates (e.g., `async () => { ... }` passed to non-Task-returning delegates). Exceptions thrown inside async void delegates crash the application because they cannot be caught.
- **[EPC35]** Avoid `.Result`, `.Wait()`, or `.GetAwaiter().GetResult()` on tasks inside async methods — use `await` instead. These cause deadlocks and defeat the purpose of async.
- **[EPC14/EPC15]** Use `ConfigureAwait(false)` in library code that does not need a synchronization context. Flag redundant `ConfigureAwait(false)` calls in application-level code (e.g., ASP.NET Core) where there is no `SynchronizationContext`.
- Prefer `ValueTask` over `Task` when the result is frequently available synchronously.
- Ensure `CancellationToken` is accepted and forwarded in async call chains.
- **[EPC33]** Do not use `Thread.Sleep` in async methods — use `await Task.Delay()` instead. `Thread.Sleep` blocks the thread and can cause thread pool starvation.
- **[EPC31]** Do not return `null` from methods with Task-like return types (`Task`, `Task<T>`, `ValueTask`). Awaiting `null` throws `NullReferenceException`. Return `Task.CompletedTask` or `Task.FromResult(...)` instead.
- **[EPC16]** Do not `await` a null-conditional expression (e.g., `await service?.ProcessAsync()`). If the left-hand side is null, the expression evaluates to `null`, and awaiting `null` throws `NullReferenceException`. Check for null explicitly first, or use `?? Task.CompletedTask`.
- **[EPC18]** Watch for `Task` instances implicitly converted to strings (e.g., in string interpolation or concatenation). This usually indicates a missing `await`, and the output will be the Task type name instead of the actual result.
- **[EPC32]** Always create `TaskCompletionSource<T>` with `TaskCreationOptions.RunContinuationsAsynchronously` to prevent deadlocks from synchronous continuations running on the completing thread.
- **[EPC36]** Do not use async delegates with `Task.Factory.StartNew` and `TaskCreationOptions.LongRunning`. The `LongRunning` flag creates a dedicated thread that is only used before the first `await`, wasting resources. Use `Task.Run` for async work instead.
- **[EPC37]** In public async methods, do not validate arguments by throwing exceptions directly — those exceptions are deferred until the Task is awaited. Instead, use a synchronous wrapper method that validates arguments eagerly, then calls a private `async` implementation (or use a local async function).
- **[EPC26]** Do not put `Task` objects in `using` blocks. Although `Task` implements `IDisposable`, calling `Dispose()` on a Task is almost never correct and can cause issues.

### Resource Management & IDisposable
- Types that own unmanaged resources or `IDisposable` members must implement `IDisposable` or `IAsyncDisposable`.
- Use `using` declarations or `using` blocks; flag missing `Dispose` calls.
- Prefer `await using` for `IAsyncDisposable`.
- **[EPC19]** Always store and dispose `CancellationTokenRegistration` objects returned by `CancellationToken.Register()`. If the cancellation token outlives the registering object, the undisposed registration causes memory leaks. Use `using var registration = token.Register(...)`.

### Exception Handling
- **[ERP021]** Use `throw;` (not `throw ex;`) to preserve stack traces when re-throwing. `throw ex;` resets the stack trace and loses the origin of the error.
- **[ERP022]** Do not silently swallow exceptions in generic catch blocks (`catch (Exception)` or bare `catch`). Always observe the exception — log it, wrap it, or re-throw it. Ensure meaningful error information is not lost.
- **[EPC12]** When catching an exception, do not rely only on `ex.Message`. The `Message` property often contains generic information. Use the full exception object (`ex.ToString()`) or pass the exception to logging frameworks to capture `InnerException`, stack trace, and other details.
- Do not catch `Exception` or `SystemException` broadly unless re-throwing or logging at a top-level boundary.
- Avoid exceptions for control flow; prefer pattern matching, `TryParse`, or result types.

### Correctness & Common Bugs
- **[EPC13]** Do not ignore return values from methods that produce important results (e.g., `string.ToUpper()`, LINQ methods, validation methods). An unobserved result is usually a bug. Use `_ = Method()` as an explicit discard if intentional.
- **[EPC34]** Methods annotated with `[MustUseResult]` (or similar attributes) must have their return values observed — ignoring them is always a bug.
- **[EPC11]** Flag suspicious `Equals()` implementations — e.g., ones that do not use the `obj` parameter, or compare only static/constant values instead of instance members.
- **[EPC30]** Watch for methods that call themselves recursively without a clear base case. Accidental recursion (e.g., a property getter calling itself instead of a backing field) causes `StackOverflowException`.
- **[EPC20]** Avoid relying on the default `ToString()` implementation (which just returns the type name). Types used in logging, interpolation, or user-facing strings should override `ToString()` or use records (which provide it automatically).

### Concurrency & Thread Safety
- **[ERP031]** Flag shared mutable state accessed from multiple threads without synchronization. Common issues:
  - `List<T>`, `Dictionary<TKey, TValue>`, `Queue<T>` are not thread-safe — use `ConcurrentBag<T>`, `ConcurrentDictionary<TKey, TValue>`, `ConcurrentQueue<T>`, or `ImmutableList<T>` instead.
  - Non-atomic `++` / `--` on shared integers — use `Interlocked.Increment` / `Interlocked.Decrement`.
  - `Random` shared across threads — use `Random.Shared` (.NET 6+) or `ThreadLocal<Random>`.
- Prefer immutable data structures and minimize shared mutable state.
- Use `lock` or `ReaderWriterLockSlim` at the smallest scope necessary. Avoid nested locks to prevent deadlocks.

### Performance
- Use `Span<T>`, `ReadOnlySpan<T>`, and `Memory<T>` for high-performance buffer operations.
- Prefer `StringBuilder` over repeated string concatenation in loops.
- Use `string.Create`, interpolated string handlers, or `StringComparison.Ordinal` for performance-sensitive string operations.
- Avoid unnecessary allocations: prefer `ArrayPool<T>`, `stackalloc`, or value types where applicable.
- Use `Frozen` collections (`FrozenDictionary`, `FrozenSet`) for read-heavy lookup tables (.NET 8+).
- Prefer `SearchValues<T>` for repeated character/byte searches (.NET 8+).
- Flag LINQ in hot paths where a simple loop would avoid allocations.
- **[EPC23]** Do not use `Enumerable.Contains` (LINQ) on `HashSet<T>` — it performs O(n) linear scan instead of O(1) hash lookup. Call the instance `.Contains()` method directly.
- **[EPC24]** Do not use structs with default `ValueType.Equals` / `ValueType.GetHashCode` as keys in hash-based collections (`Dictionary`, `HashSet`). The default implementations use reflection and are extremely slow. Implement `IEquatable<T>` or use `record struct`.
- **[EPC25]** Avoid calling the default `Equals` or `GetHashCode` on structs in general. Override them or use `record struct` (C# 10+) for automatic efficient implementations.

### Security
- Never concatenate user input into SQL — use parameterized queries or an ORM.
- Validate and sanitize all external inputs (HTTP parameters, file paths, deserialized data).
- Use `SecureString` or protected configuration for secrets; never hard-code credentials.
- Prefer `System.Security.Cryptography` APIs; flag use of obsolete algorithms (MD5, SHA1 for security purposes, DES).
- Ensure `HttpClient` instances validate TLS certificates; flag disabled certificate checks.

### Dependency Injection & Service Lifetimes
- Prefer constructor injection over service locator patterns.
- Ensure service lifetimes are correct: avoid injecting `Scoped` services into `Singleton` services (captive dependency).
- Register `HttpClient` via `IHttpClientFactory`, not as a singleton.

### Collections & LINQ
- Use the appropriate collection type: `IReadOnlyList<T>` for return types, `List<T>` for internal mutation, `HashSet<T>` for membership checks.
- Prefer `Collection Expressions` (`[1, 2, 3]`) when targeting C# 12+.
- Avoid multiple enumeration of `IEnumerable<T>` — materialize with `.ToList()` or `.ToArray()` when needed.
- Use `Any()` instead of `Count() > 0` for existence checks.

### Entity Framework & Data Access
- Avoid N+1 query problems — use `.Include()` for eager loading or explicit projection.
- Use `AsNoTracking()` for read-only queries.
- Avoid calling `.ToList()` or `.ToArray()` prematurely, which forces client-side evaluation.
- Prefer `ExecuteUpdateAsync` / `ExecuteDeleteAsync` for bulk operations (.NET 7+).

### EventSource
- **[ERP041]** `EventSource`-derived classes should be `sealed`. Unsealed EventSource classes can cause runtime issues with ETW event generation.
- **[ERP042]** Verify `EventSource` implementations are correct — method signatures, event IDs, and keywords must follow EventSource conventions.

### Code Coverage Attributes
- **[EPC28]** Do not apply `[ExcludeFromCodeCoverage]` to partial classes — it may not cover all parts of the class and gives a false sense of coverage.
- **[EPC29]** Always provide a justification message when using `[ExcludeFromCodeCoverage(Justification = "...")]` to document why code is excluded from coverage.

### Testing Considerations
- Flag tightly coupled code that is difficult to unit test (e.g., `new`-ing up dependencies directly instead of injecting them).
- Ensure public APIs have clear contracts that are testable.

### Modern C# & .NET Idioms
- Prefer **primary constructors** (C# 12+) for simple DI and DTOs.
- Use **records** for immutable data transfer objects (also gives correct `Equals`, `GetHashCode`, and `ToString` for free).
- Use **record structs** (C# 10+) for value types that need proper equality and hashing without manual implementation.
- Prefer **pattern matching** (`is`, `switch` expressions) over type-casting chains.
- Use **file-scoped namespaces** to reduce nesting.
- Use **raw string literals** (`"""..."""`) for multi-line strings.
- Prefer **target-typed `new()`** when the type is evident from context.
- Use `required` modifier for properties that must be set at initialization (C# 11+).

## Output Format

Present findings grouped by file, with severity icons, rule IDs (when applicable), and concise explanations:

```
### `path/to/File.cs`

🔴 **Line 42 — SQL Injection risk**
User input is concatenated directly into a SQL command string.
```csharp
// Before
var cmd = new SqlCommand($"SELECT * FROM Users WHERE Name = '{name}'");

// After
var cmd = new SqlCommand("SELECT * FROM Users WHERE Name = @name");
cmd.Parameters.AddWithValue("@name", name);
```

🟠 **Line 87 — Potential deadlock [EPC35]**
`.Result` is called on an async method inside an async context. Use `await` instead.
```csharp
// Before
var data = GetDataAsync().Result;

// After
var data = await GetDataAsync();
```

🟠 **Line 103 — Null Task return [EPC31]**
Returning `null` from a `Task<T>` method will cause `NullReferenceException` when awaited.
```csharp
// Before
public Task<string> GetDataAsync() => condition ? FetchAsync() : null;

// After
public Task<string> GetDataAsync() => condition ? FetchAsync() : Task.FromResult<string>(null);
```

🟡 **Line 150 — Deferred argument validation [EPC37]**
Argument validation in a public async method won't throw until the Task is awaited. Use a synchronous wrapper.
```csharp
// Before
public async Task<string> ReadAsync(string path)
{
    ArgumentNullException.ThrowIfNull(path);
    return await File.ReadAllTextAsync(path);
}

// After
public Task<string> ReadAsync(string path)
{
    ArgumentNullException.ThrowIfNull(path);
    return ReadAsyncCore(path);
}
private async Task<string> ReadAsyncCore(string path)
{
    return await File.ReadAllTextAsync(path);
}
```
```

If no issues are found, confirm the code looks good and briefly note any positive patterns observed.

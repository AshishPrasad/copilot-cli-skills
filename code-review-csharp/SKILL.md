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

The checklist rules are organized into separate files for easy maintenance:

| File | Category |
|------|----------|
| [naming-conventions.md](naming-conventions.md) | Naming & Conventions |
| [null-safety.md](null-safety.md) | Null Safety & Defensive Coding |
| [async-await.md](async-await.md) | Async / Await |
| [resource-management.md](resource-management.md) | Resource Management & IDisposable |
| [exception-handling.md](exception-handling.md) | Exception Handling |
| [correctness.md](correctness.md) | Correctness & Common Bugs |
| [concurrency.md](concurrency.md) | Concurrency & Thread Safety |
| [performance.md](performance.md) | Performance |
| [security.md](security.md) | Security |
| [dependency-injection.md](dependency-injection.md) | Dependency Injection & Service Lifetimes |
| [collections-linq.md](collections-linq.md) | Collections & LINQ |
| [entity-framework.md](entity-framework.md) | Entity Framework & Data Access |
| [eventsource.md](eventsource.md) | EventSource |
| [code-coverage.md](code-coverage.md) | Code Coverage Attributes |
| [testing.md](testing.md) | Testing Considerations |
| [solid-principles.md](solid-principles.md) | SOLID Design Principles |
| [modern-csharp.md](modern-csharp.md) | Modern C# & .NET Idioms |

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

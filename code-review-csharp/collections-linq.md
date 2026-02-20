# Collections & LINQ

- Use the appropriate collection type: `IReadOnlyList<T>` for return types, `List<T>` for internal mutation, `HashSet<T>` for membership checks.
- Prefer `Collection Expressions` (`[1, 2, 3]`) when targeting C# 12+.
- Avoid multiple enumeration of `IEnumerable<T>` — materialize with `.ToList()` or `.ToArray()` when needed.
- Use `Any()` instead of `Count() > 0` for existence checks.

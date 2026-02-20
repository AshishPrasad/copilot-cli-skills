# Performance

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

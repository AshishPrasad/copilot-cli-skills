# Null Safety & Defensive Coding

- Prefer **nullable reference types** (`#nullable enable`) and avoid `null` where possible.
- Guard public API parameters with `ArgumentNullException.ThrowIfNull()` (.NET 6+) or null checks.
- Avoid `null` return values from methods; prefer empty collections, `Option<T>`, or `default`.
- Flag unguarded dereferences of nullable types.

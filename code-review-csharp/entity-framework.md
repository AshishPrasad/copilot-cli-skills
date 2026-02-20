# Entity Framework & Data Access

- Avoid N+1 query problems — use `.Include()` for eager loading or explicit projection.
- Use `AsNoTracking()` for read-only queries.
- Avoid calling `.ToList()` or `.ToArray()` prematurely, which forces client-side evaluation.
- Prefer `ExecuteUpdateAsync` / `ExecuteDeleteAsync` for bulk operations (.NET 7+).

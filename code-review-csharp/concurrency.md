# Concurrency & Thread Safety

- **[ERP031]** Flag shared mutable state accessed from multiple threads without synchronization. Common issues:
  - `List<T>`, `Dictionary<TKey, TValue>`, `Queue<T>` are not thread-safe — use `ConcurrentBag<T>`, `ConcurrentDictionary<TKey, TValue>`, `ConcurrentQueue<T>`, or `ImmutableList<T>` instead.
  - Non-atomic `++` / `--` on shared integers — use `Interlocked.Increment` / `Interlocked.Decrement`.
  - `Random` shared across threads — use `Random.Shared` (.NET 6+) or `ThreadLocal<Random>`.
- Prefer immutable data structures and minimize shared mutable state.
- Use `lock` or `ReaderWriterLockSlim` at the smallest scope necessary. Avoid nested locks to prevent deadlocks.

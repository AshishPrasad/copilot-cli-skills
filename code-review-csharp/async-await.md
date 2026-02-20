# Async / Await

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

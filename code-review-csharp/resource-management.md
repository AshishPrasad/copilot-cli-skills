# Resource Management & IDisposable

- Types that own unmanaged resources or `IDisposable` members must implement `IDisposable` or `IAsyncDisposable`.
- Use `using` declarations or `using` blocks; flag missing `Dispose` calls.
- Prefer `await using` for `IAsyncDisposable`.
- **[EPC19]** Always store and dispose `CancellationTokenRegistration` objects returned by `CancellationToken.Register()`. If the cancellation token outlives the registering object, the undisposed registration causes memory leaks. Use `using var registration = token.Register(...)`.

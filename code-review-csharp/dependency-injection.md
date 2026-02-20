# Dependency Injection & Service Lifetimes

- Prefer constructor injection over service locator patterns.
- Ensure service lifetimes are correct: avoid injecting `Scoped` services into `Singleton` services (captive dependency).
- Register `HttpClient` via `IHttpClientFactory`, not as a singleton.

# Testing Considerations

- Flag tightly coupled code that is difficult to unit test (e.g., `new`-ing up dependencies directly instead of injecting them).
- Ensure public APIs have clear contracts that are testable.

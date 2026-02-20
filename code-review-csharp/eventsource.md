# EventSource

- **[ERP041]** `EventSource`-derived classes should be `sealed`. Unsealed EventSource classes can cause runtime issues with ETW event generation.
- **[ERP042]** Verify `EventSource` implementations are correct — method signatures, event IDs, and keywords must follow EventSource conventions.

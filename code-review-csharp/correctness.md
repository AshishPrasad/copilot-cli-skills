# Correctness & Common Bugs

- **[EPC13]** Do not ignore return values from methods that produce important results (e.g., `string.ToUpper()`, LINQ methods, validation methods). An unobserved result is usually a bug. Use `_ = Method()` as an explicit discard if intentional.
- **[EPC34]** Methods annotated with `[MustUseResult]` (or similar attributes) must have their return values observed — ignoring them is always a bug.
- **[EPC11]** Flag suspicious `Equals()` implementations — e.g., ones that do not use the `obj` parameter, or compare only static/constant values instead of instance members.
- **[EPC30]** Watch for methods that call themselves recursively without a clear base case. Accidental recursion (e.g., a property getter calling itself instead of a backing field) causes `StackOverflowException`.
- **[EPC20]** Avoid relying on the default `ToString()` implementation (which just returns the type name). Types used in logging, interpolation, or user-facing strings should override `ToString()` or use records (which provide it automatically).

# Exception Handling

- **[ERP021]** Use `throw;` (not `throw ex;`) to preserve stack traces when re-throwing. `throw ex;` resets the stack trace and loses the origin of the error.
- **[ERP022]** Do not silently swallow exceptions in generic catch blocks (`catch (Exception)` or bare `catch`). Always observe the exception — log it, wrap it, or re-throw it. Ensure meaningful error information is not lost.
- **[EPC12]** When catching an exception, do not rely only on `ex.Message`. The `Message` property often contains generic information. Use the full exception object (`ex.ToString()`) or pass the exception to logging frameworks to capture `InnerException`, stack trace, and other details.
- Do not catch `Exception` or `SystemException` broadly unless re-throwing or logging at a top-level boundary.
- Avoid exceptions for control flow; prefer pattern matching, `TryParse`, or result types.

# Security

- Never concatenate user input into SQL — use parameterized queries or an ORM.
- Validate and sanitize all external inputs (HTTP parameters, file paths, deserialized data).
- Use `SecureString` or protected configuration for secrets; never hard-code credentials.
- Prefer `System.Security.Cryptography` APIs; flag use of obsolete algorithms (MD5, SHA1 for security purposes, DES).
- Ensure `HttpClient` instances validate TLS certificates; flag disabled certificate checks.

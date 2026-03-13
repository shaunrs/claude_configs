# Security

## Principles

- **Never log sensitive data** - No keys, tokens, passwords, plaintexts, or intermediate cryptographic values in logs. When in doubt, don't log it.
- **Validate at system boundaries** - All external input (user input, API responses, file contents) gets validated. Internal code trusts internal code.
- **Fail closed** - On any security error, reject the operation entirely. Don't continue with partial data or fall back to insecure defaults.
- **No secrets in error messages** - Client-facing errors use codes and generic messages. Internal details (stack traces, variable values) stay server-side.

## Memory and Data Handling

- **Zeroize sensitive memory** - Keys and secrets should be explicitly cleared when no longer needed:
  - Rust: `#[derive(ZeroizeOnDrop)]` or explicit `zeroize()`
  - JavaScript/TypeScript: overwrite buffers, avoid string storage for secrets
  - Use library-provided zeroization over manual `.fill(0)`
- **Minimize sensitive data lifetime** - Decrypt/unwrap secrets as late as possible, clear as early as possible.

## Authentication and Authorization

- **Server-side validation is authoritative** - Client-side checks are for UX only. Never trust client-provided auth state.
- **Use established patterns** - JWT validation, CSRF protection (Fetch Metadata headers), PKCE for OAuth. Don't invent custom auth schemes.

## Environment and Configuration

- **Validate environment at startup** - Missing or invalid secrets should fail fast, not at first use.
- **Never commit secrets** - Use environment variables, secret managers, or encrypted config. Add secret patterns to `.gitignore`.
- **Separate server and client config** - Server-only variables should error if accessed client-side.

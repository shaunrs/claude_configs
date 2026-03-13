# Testing

## Test Pyramid

- **Prefer fast tests** - Test at the fastest layer that can verify the behavior. Reserve slow, expensive tests (E2E, integration) for what truly cannot be tested as a unit test.
- **Unit tests first** - Pure functions, isolated logic, schema validation.
- **Integration tests second** - Component interactions, API contracts.
- **E2E tests last** - Critical user flows only, not exhaustive coverage.

## Test-Driven Development

- **Write tests for new features** - All new functionality should have corresponding tests.
- **Keep tests in sync** - Delete tests when deleting code. Update tests when changing behavior.
- **Never test external library functionality** - Tests verify your code's behavior, not third-party libraries. Trust well-maintained dependencies to work correctly.

## Performance Requirements

- **Linting must complete in <30 seconds** - This is a hard requirement. When linting exceeds this threshold, investigate the root cause rather than accepting degradation.
- **Investigate slowdowns** - When tests or CI slow down, find the root cause. Don't accept "it just takes longer now."

## CI/CD Principles

- **No dev-only workarounds that mask production issues** - When CI needs differ from local development (e.g., generated types required for linting), prefer standard solutions:
  - **Use build artifacts** - Build expensive outputs once, share via artifacts to downstream jobs.
  - **Never commit stubs or mocks to source** - A stub for generated code could mask a missing build step locally, causing runtime failures in production.
- **Guiding question** - "If someone forgets a build step, will the system catch it?" The answer must be yes.

## Coverage

- **Exclude dev stubs from coverage** - When creating development stubs, mocks, or test-only implementations, add them to the coverage `omit` list immediately. These are not production code.

## Test Organization

- **One canonical test file per feature** - Each endpoint or feature gets its own test file. Don't scatter tests for the same feature across multiple files.
- **Shared helpers in a dedicated file** - Common test utilities (`setupSession()`, `encryptPayload()`, `createMockUser()`) live in `helpers.ts` or `conftest.py`, not duplicated across test files.
- **Fixtures for reusable setup** - Use framework fixtures (pytest fixtures, Jest beforeEach) for common setup. Register shared mocks in a central setup file.

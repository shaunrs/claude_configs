# Testing

## Test Pyramid

- **Prefer fast tests** - Test at the fastest layer that can verify the behavior. Reserve slow, expensive tests (E2E, integration) for what truly cannot be tested as a unit test.
- **Unit tests first** - Pure functions, isolated logic, schema validation. Structure code to maximize what can be tested at this level.
- **Integration tests second** - Component interactions, API contracts.
- **E2E tests last** - Critical user flows only. E2E tests are expensive to write, slow to run, and fragile to maintain. Treat them as a scarce resource.
  - **Justification required** - Before adding an E2E test, ask: "What does this test that a cheaper test could not achieve?" If unit or integration tests provide equivalent coverage, use those instead.

## Test Quality

- **One behavior per test** - Each test verifies one logical behavior. Multiple assertions verifying different facets of the same behavior are fine (status code AND body AND headers from one HTTP call). Split into separate tests when assertions test independent behaviors or require different setup.
- **Arrange-Act-Assert structure** - Separate setup, execution, and verification with blank lines. One Act (execution) per test. If your Act is multiple lines, you're testing a workflow, not a unit.
- **Test names as documentation** - When a test fails, the name alone should explain what broke without reading the body. Use `describe` for the unit, `context` (or nested `describe`) for the scenario, `it` for the expected behavior. Full concatenation should read as a sentence.
- **Avoid fragile selectors** - Don't couple tests to UI text, element order, or implementation details that change frequently. Use stable identifiers (data-testid, roles) over CSS classes or exact string matches.
- **DRY applies to tests** - Avoid code duplication in tests just as in production code. Extract shared setup, assertions, and utilities into helpers.
- **Factories over fixtures for domain objects** - Use factories (Fishery, FactoryBot, factory_boy) for objects with many valid states. Factories show exactly which attributes matter per test. Reserve fixtures for stable reference data (countries, roles, permissions) that rarely changes.
  - **Minimal defaults** - factories produce the simplest valid object. Use traits or overrides for variations.
  - **Use `build` over `create`** - in-memory objects over persisted ones when persistence isn't the behavior under test. This is a primary cause of slow tests.

## Mocking Strategy

- **Always mock unmanaged dependencies** - External APIs, third-party services, payment gateways, and any system you don't control must be mocked. These calls are non-deterministic, slow, costly, and risk creating unwanted side effects.
- **Always mock state-mutating operations** - File system writes, database mutations, cache invalidations, and similar side effects should be mocked unless that specific mutation is the behavior under test.
- **Don't mock what you don't own** - Never mock third-party library internals directly. Instead, wrap external dependencies in your own adapter and mock that interface. This isolates your tests from upstream API changes and clarifies what you actually use from the dependency.
- **Don't mock internal collaborators** - Code you own and control should generally use real implementations in tests. Mocking your own classes couples tests to implementation details and breaks during refactoring.
- **State verification over behavior verification** - Prefer asserting on the result or final state rather than verifying specific method calls were made. Behavior verification creates brittle tests coupled to implementation.
- **Keep mocks simple** - If mock setup is painful, treat it as design feedback. Complex mocking often indicates the code under test has too many responsibilities or unclear boundaries.
- **Clean up mocks in `afterEach`** - Use `vi.restoreAllMocks()` (Vitest), `jest.restoreAllMocks()` (Jest), or equivalent. Without cleanup, mock state leaks between tests causing flaky failures. Always restore fake timers (`vi.useRealTimers()`) in teardown.

## Test-Driven Development

- **Write tests for new features** - All new functionality should have corresponding tests.
- **Keep tests in sync** - Delete tests when deleting code. Update tests when changing behavior.
- **Never test external library functionality** - Tests verify your code's behavior, not third-party libraries. Trust well-maintained dependencies to work correctly.

## Performance Requirements

- **Full test suite must complete in <60 seconds** - The time between pushing to git and being able to merge should be short. Developers shouldn't wait around for tests to finish. When adding tests pushes the suite over this limit, optimize before merging.
- **Local tests must complete in <60 seconds** - Tests should be reasonable to run on commit/push hooks without significantly slowing down developers or AI agents. When tests exceed this threshold, investigate the root cause.
- **Linting must complete in <30 seconds** - Linting runs on every save and commit. It must stay fast enough that it never becomes something to avoid. When linting exceeds this threshold, investigate the root cause rather than accepting degradation.
- **Investigate slowdowns** - When tests or CI slow down, find the root cause. Don't accept "it just takes longer now."
- **Common speed killers** - Network I/O (even to localhost), database operations per test (use transactions + rollback), file system I/O (use in-memory alternatives), process spawning, sleep/wait statements, heavy factory chains that implicitly create many associated objects. Profile regularly to catch regressions.

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

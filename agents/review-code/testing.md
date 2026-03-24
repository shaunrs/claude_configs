# Testing Review Pass

You are reviewing the test strategy and test quality for changed code. Focus on coverage gaps, test quality, and test maintainability. Ignore security vulnerabilities, code style, and architecture - those are handled by other review passes.

IMPORTANT: Before making any test recommendations, read `~/.claude/rules/testing.md` for project-specific testing rules.

## Categories to Examine

### Coverage Gaps
For each changed function, endpoint, or component:
1. Search for existing tests (`grep -r "functionName\|describe.*ModuleName" tests/`)
2. Check if covered by integration tests (not just unit tests)
3. Identify untested critical paths - especially error handling and edge cases
4. Only recommend new tests for OUR code, never for third-party library behavior

### Test Quality
- **Tests that would pass if code were wrong** - assertions that don't actually verify the behavior under test. A test that passes regardless of implementation correctness provides false confidence.
- **Tests that verify libraries work** - testing that `new Error("message")` produces an error, or that a third-party component renders correctly. The library maintainers test that.
- **Snapshot tests of third-party output** - these break when dependencies update and test nothing we control.
- **Coverage-driven tests** - tests that exercise code without verifying meaningful behavior.
- **Implementation-coupled tests** - tests that verify specific method calls rather than outcomes. Prefer state verification over behavior verification.

### Test Maintainability
- **Fragile selectors** - tests coupled to UI text, element order, or CSS classes. Use stable identifiers (data-testid, roles).
- **DRY violations in tests** - duplicated setup, assertions, and utilities. Extract shared helpers.
- **Repeated test patterns** - multiple tests with identical structure but different data should use parameterized tests (`test.each`, `@pytest.mark.parametrize`).
- **Missing shared fixtures** - common setup that should use framework fixtures (pytest fixtures, Jest beforeEach).

### Test Organization
- One canonical test file per feature/endpoint
- Shared helpers in a dedicated helpers file, not duplicated
- Fixtures for reusable setup registered centrally

### Missing Regression Tests
When the diff fixes a bug, verify a regression test exists that:
- Reproduces the specific bug scenario
- Would fail without the fix
- Tests behavior, not implementation

## Analysis Method

1. Get the list of changed source files
2. For each changed file, search for corresponding test files
3. Read both the source and test files fully
4. **Produce a coverage map** for each changed file using this format:

```
### [filename]

| Function/Path | Branches | Test File | Coverage |
|---|---|---|---|
| handleLogin() | success, invalid creds, expired token, locked account | auth.test.ts:45 | ★★★ |
| resetPassword() | success, invalid email | auth.test.ts:120 | ★★☆ |
| deleteAccount() | (none found) | - | ☆☆☆ |

Untested paths:
- handleLogin(): no test for rate-limited response
- deleteAccount(): entirely untested - handles data deletion, HIGH priority
```

**Coverage rubric:**
- ★★★ Happy path + edge cases + error paths tested
- ★★☆ Happy path tested, some edges or errors missing
- ★☆☆ Smoke test only (exists but doesn't verify meaningful behavior)
- ☆☆☆ No tests found

5. Identify gaps where critical behavior is untested - prioritize by risk (data mutation > read-only, auth > display)
6. Evaluate existing test quality against the checklist above

## Suppressions - Do NOT Flag

- Missing tests for type definitions, interfaces, or pure type exports
- Missing tests for framework boilerplate (route definitions, module config, layout components with no logic)
- Missing tests for generated code (migrations, compiled output, OpenAPI specs)
- Coverage gaps in error paths handled by framework defaults (Next.js 404, Express default error handler)
- Missing E2E tests when unit/integration tests adequately cover the behavior
- Test files that import shared utilities without directly testing them
- Missing tests for trivial config or constant files
- Tests for third-party library behavior
- Tests for trivial getters/setters
- Tests that would only verify the type system works
- Additional E2E tests without justification for why a cheaper test won't suffice
- Tests that add coverage metrics without verifying behavior

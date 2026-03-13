# Code Style

## Core Principles

- **DRY (Don't Repeat Yourself)** - Avoid code duplication. Validation schemas should be shared from a single source of truth - never duplicate Zod (or similar) schemas.
- **YAGNI (You Aren't Gonna Need It)** - Only build what is needed now. Examples of premature optimization to avoid:
  - Functions, utilities, or abstractions for features that don't exist yet
  - UI elements (buttons, toggles, menus) with no backing functionality
  - State variables never consumed by business logic
  - Event handlers that only log to console or do nothing
  - Theme tokens, fonts, or design system values no component uses
  - API endpoints or database columns for planned-but-not-built features
  - Configuration options with only one possible value
  - Test mocks for code paths that don't exist
  - Commented-out code "for later"
  - Generic interfaces when only one implementation exists

## Dependencies and Structure

- **Explicit dependencies over hidden ones** - Pass dependencies as parameters rather than reaching for globals or hardcoding instantiation. This enables easier testing and loose coupling.
  - Use DI frameworks when the codebase/framework convention calls for it (Angular, NestJS, Spring), but don't introduce DI containers just for their own sake.
  - In functional or simpler codebases, plain parameter passing achieves the same benefits without overhead.
- **No convenience re-exports** - Import directly from the source module, not through intermediate files. Re-exports are only acceptable in:
  - Barrel files (`index.ts`) that define a module's public API
  - When refactoring to hide internal restructuring from external consumers
  - When adding transformation or abstraction (not pass-through)

## Functions vs Classes

- **Functions by default** - Use standalone functions unless multiple functions share mutable module-level state with a lifecycle (e.g., database connections, session handles).
- **Classes for shared mutable state** - When state has a lifecycle (open/close, connect/disconnect), encapsulate in a class to make coupling explicit.
- **Don't use classes just for grouping** - Related functions don't need a class wrapper.

## UI Design

- **Mobile-first** - Always build UI for mobile first, then optimize for desktop.
- **Progressive disclosure** - Start with the simplest possible interface showing only essential options. Reveal advanced features only when the user needs them.

## Code Quality

- **No magic numbers** - Always use descriptive constants instead of literal numbers. Define constants with clear names that explain the value's purpose (e.g., `PCR_HEX_LENGTH = 96` instead of `96`).
- **Comments as final state** - Write comments as though the code was correct from the start. Never reference previous implementations, fixes, or changes (avoid "now uses X instead of Y", "fixed to use", "changed from"). Comments describe what the code does, not its history.
- **Data manipulation should be idempotent** - Avoid overly defensive coding practices.

## Error Handling

- **Typed errors in libraries, context in applications** - Libraries define specific error types (e.g., `CryptoError`, `ValidationError`). Applications wrap with context for debugging (e.g., `thiserror` vs `anyhow` in Rust, custom exceptions vs generic in Python/TS).
- **Error helpers for consistency** - Use shared error response helpers (`authError()`, `validationError()`, `notFoundError()`) rather than constructing error responses ad-hoc throughout the codebase.
- **Generic errors for clients** - Client-facing errors use codes and safe messages. Internal details (stack traces, variable values) stay server-side.

## Naming Conventions

- **Cryptographic keys** - Use `PublicKey`/`PrivateKey` suffixes (never `Pk`/`Sk` or ambiguous abbreviations like `_pk`/`_sk`).
- **Auth terminology** - Use `login`, `signup`, `logout` (not signIn/signUp/signOut).

## Typing

- **No `any` types** - They should fail linting (`no-explicit-any`).
- **`unknown` is not a substitute for `any`** - Only use `unknown` when you have exhausted all types that exist within the system.
- **Prefer type inference** - Let the compiler infer types. Avoid explicit annotations and casting when inference works.
- **Use `import type`** - For type-only imports (enforced in strict TypeScript projects).
- **Avoid complex type casting** - Fix type errors and have them properly inferred where possible.

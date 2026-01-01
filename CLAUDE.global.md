# Claude Rules

## Communication
- If I'm wrong, tell me so. I do not need to be flattered.
- When asking clarifying questions, always provide a few potential solutions based on your analysis and a suggested path forward

## Documentation
- After completing planning mode, always write the full plan as a PRD with filename formatted ./.docs/PRD-[date]-[unique_name].md - replacing [date] with the output of `date -I`
- When creating a PRD, ALWAYS provide User Stories and Mermaid diagrams to guide the reader through the flow of the system
- When asked for a technical design doc, I expect bullet-points and descriptions of the problems and our solutions, not code blocks. This should be a Tech Design that one might do as the final part of product planning before implementation begins. At that stage the programming language may still not be known.

### README must be simple, concise and scannable.
  Key highlights:
  - Super simple: Keep it to a short list of key libraries, commands and code quality tooling.
  - Stack section: One-line descriptions for each key library
  - Commands: Just the essentials (dev, build, lint, type-check, validate)
  - Code Quality: Bullet points covering linting, formatting, pre-commit, and commits

## Process

### Repository Management
- When creating a new project, ALWAYS add common build, test, and linting scripts to an appropriate shortcut command (e.g. for React projects, add scripts in package.json)
- When adding dependencies, do not manually edit the project configuration, but use the relevant `add` command such as `pnpm add` or `flutter add` to install the latest version of the dependency
- When using bash commands to move files, always use `git mv` to ensure git consistency
- For any new project, always add a .gitignore file with the following basic content:
    ```
    # Claude
    .claude
    .specstory

    # macOS
    .DS_Store
    *.pem
    ```

### Linting and Type Checking
- After making any code change, you must run the linter and any typecheckers to validate the code
- Always run `pnpm lint --fix` first, it reduces the time to a successful linting pass

### Summarizing Changes
- After completing a phase of work, please list all of the filenames that had changes applied

## Coding Style

- When creating new features, or updating existing ones, always check to ensure @CLAUDE.md remains accurate
- Validation schemas should always be shared from a single source of truth, never duplicate zod (or similar) schemas
- If I tell you something about the code, ALWAYS verify it is true before executing on it
- Always adhere to DRY principles and avoid code duplication as far as practical
- **Follow YAGNI (You Aren't Gonna Need It)** - Only build what is needed right now. Examples of premature optimization to avoid:
  - Functions, utilities, or abstractions for features that don't exist yet
  - UI elements (buttons, toggles, menus) with no backing functionality
  - State variables that are never consumed by business logic
  - Event handlers that only log to console or do nothing
  - Theme tokens, fonts, or design system values that no component uses
  - API endpoints or database columns for planned-but-not-built features
  - Configuration options that have only one possible value
  - Test mocks for code paths that don't exist
  - Commented-out code "for later"
  - Generic interfaces when only one implementation exists
- Always build UI for mobile first, then optimize it for Desktop

- **No convenience re-exports** - Import directly from the source module, not through intermediate files. Re-exports are only acceptable in:
  - Barrel files (`index.ts`) that define a module's public API
  - When refactoring to hide internal restructuring from external consumers
  - When adding transformation or abstraction (not pass-through)

- **No magic numbers** - Always use descriptive constants instead of literal numbers in code. Define constants with clear names that explain the value's purpose (e.g., `PCR_HEX_LENGTH = 96` instead of `96`)

### Data Manipulation
- Always implement data manipulation in an idempotent model, avoid overly defensive coding practices

## Typing and type checking
- `any` types are not allowed, they should fail linting in all of our repositories (no-explicit-any)
- `unknown` is not a suitable substitute for `any`
- Only use the `unknown` type when you have exhausted all types that currently exist within the system
- Let's avoid complex type casting, we should fix type errors and have them properly inferred where possible


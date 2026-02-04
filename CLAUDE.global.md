# Claude Rules

## Communication
- If I'm wrong, tell me so. I do not need to be flattered.
- When asking clarifying questions, always provide a few potential solutions based on your analysis and a suggested path forward.

## Documentation

- After completing planning mode, always write the full plan as a PRD with filename formatted `./.docs/PRD-[date]-[unique_name].md` (replacing [date] with the output of `date -I`)
- After implementing a PRD, rename the document using `mv .docs/PRD-[date]-{,DONE-}[unique_name].md` - keeping the existing date and unique_name
- When creating a PRD, ALWAYS provide User Stories and Mermaid diagrams to guide the reader through the flow of the system
- When asked for a technical design doc, I expect bullet-points and descriptions of the problems and our solutions, not code blocks. This should be a Tech Design that one might do as the final part of product planning before implementation begins. At that stage the programming language may still not be known.

### README Requirements
README files must be simple, concise and scannable:
- **Stack section** - One-line descriptions for each key library
- **Commands** - Just the essentials (dev, build, lint, type-check, validate)
- **Code Quality** - Bullet points covering linting, formatting, pre-commit, and commits
- **License** - No need to include a License section, unless explicitly requested by the user. If so you must clarify the license to use.

## Process

### Repository Management
- When creating a new project, ALWAYS add common build, test, and linting scripts to an appropriate shortcut command (e.g. for React projects, add scripts in package.json)
- When adding dependencies, do not manually edit the project configuration, but use the relevant `add` command such as `pnpm add` or `flutter add` to install the latest version of the dependency
- When using bash commands to move files, always use `git mv` to ensure git consistency
- For any new project, always add a `.gitignore` file with the following basic content:
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
- Always run `pnpm lint --fix` first, as it reduces the time to a successful linting pass

### Summarizing Changes
- After completing a phase of work, list all of the filenames that had changes applied

### Learning from Mistakes
When a mistake is made and a resolution is determined, add a rule to CLAUDE.md to prevent the same mistake from happening again. Choose the appropriate file based on scope:
- `~/.claude/CLAUDE.md` - Language-agnostic rules that apply to all projects (e.g., explicit dependencies, functions by default, no convenience re-exports)
- `./CLAUDE.md` - Project or language-specific rules (e.g., database connection patterns, auth context structure, framework conventions)

### Elegant Solution Refinement
**MANDATORY:** At the end of any session involving large coding work (500+ lines of code written/modified) OR long debugging/fixing session (5+ minutes of investigation), you MUST ask the user:

> "Now that we have a working solution with full understanding of the problem, would you like me to refactor this into a more elegant implementation?"

**What "elegant" means:**
- **Single source of truth** - Consolidate duplicated data/logic into one authoritative location
- **Explicit dependencies** - Make implicit relationships visible through imports/parameters
- **Minimal surface area** - Remove intermediate layers that don't add value
- **Self-documenting structure** - File/function names that explain the architecture

Additionally, review against all principles in the Coding Style section below (DRY, YAGNI, functions by default, no magic numbers, etc.).

**Why this matters:** Working solutions developed during debugging often accumulate accidental complexity. Refining while context is fresh produces cleaner, more maintainable code.

## Coding Style

- When creating new features, or updating existing ones, always check to ensure CLAUDE.md remains accurate
- Validation schemas should always be shared from a single source of truth, never duplicate zod (or similar) schemas
- If I tell you something about the code, ALWAYS verify it is true before executing on it
- Always adhere to **DRY** (Don't Repeat Yourself) principles and avoid code duplication as far as practical
- Follow **YAGNI** (You Aren't Gonna Need It) - Only build what is needed right now. Examples of premature optimization to avoid:
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
- **Prefer explicit dependencies over hidden ones** - Pass dependencies as parameters rather than reaching for globals or hardcoding instantiation. This enables easier testing and loose coupling.
  - Use DI frameworks when the codebase/framework convention calls for it (Angular, NestJS, Spring), but don't introduce DI containers just for their own sake.
  - In functional or simpler codebases, plain parameter passing achieves the same benefits without the overhead.
- **Functions by default, classes for shared mutable state** - Use standalone functions unless multiple functions share mutable module-level state with a lifecycle (e.g., database connections, session handles). In that case, encapsulate in a class to make the coupling explicit. Don't use classes just for grouping related functions.
- Always build UI for mobile first, then optimize it for Desktop
- **Use progressive disclosure** - Start with the simplest possible interface showing only essential options. Reveal advanced features, settings, or information only when the user needs them. This reduces cognitive load and makes applications approachable without sacrificing power-user functionality.
- **No convenience re-exports** - Import directly from the source module, not through intermediate files. Re-exports are only acceptable in:
  - Barrel files (`index.ts`) that define a module's public API
  - When refactoring to hide internal restructuring from external consumers
  - When adding transformation or abstraction (not pass-through)
- **No magic numbers** - Always use descriptive constants instead of literal numbers in code. Define constants with clear names that explain the value's purpose (e.g., `PCR_HEX_LENGTH = 96` instead of `96`)

### Data Manipulation
- Always implement data manipulation in an idempotent model, avoid overly defensive coding practices

## Typing and Type Checking
- `any` types are not allowed, they should fail linting in all of our repositories (no-explicit-any)
- `unknown` is not a suitable substitute for `any`
- Only use the `unknown` type when you have exhausted all types that currently exist within the system
- Let's avoid complex type casting, we should fix type errors and have them properly inferred where possible

# Workflow

## Summarizing Changes

- **List modified files** - After completing a phase of work, list all filenames that had changes applied.

## Learning from Mistakes

When a mistake is made and a resolution is determined, add a rule to prevent recurrence. Choose the appropriate location:

- **`~/.claude/CLAUDE.md` or `~/.claude/rules/`** - Language-agnostic rules that apply to all projects (e.g., explicit dependencies, functions by default, no convenience re-exports).
- **`./CLAUDE.md`** - Project or language-specific rules (e.g., database connection patterns, auth context structure, framework conventions).

## Elegant Solution Refinement

**At the end of any session involving large coding work (500+ lines written/modified) OR long debugging session (5+ minutes of investigation), ask:**

> "Now that we have a working solution with full understanding of the problem, would you like me to scrap this and build the most elegant solution?"

**What "elegant" means:**
- **Single source of truth** - Consolidate duplicated data/logic into one authoritative location.
- **Explicit dependencies** - Make implicit relationships visible through imports/parameters.
- **Minimal surface area** - Remove intermediate layers that don't add value.
- **Self-documenting structure** - File/function names that explain the architecture.

If this is already the most elegant solution, say so clearly.

**Why this matters:** Working solutions developed during debugging often accumulate accidental complexity. Refining while context is fresh produces cleaner code.

## Continuous Sync

- **Keep CLAUDE.md accurate** - When creating or updating features, verify CLAUDE.md still reflects reality.
- **Keep tests in sync with code** - Delete tests when deleting code; update tests when changing behavior.
- **Update E2E tests after route changes** - Page objects must match actual routes.

## Planning

- **Test proposals against use cases** - Before finalizing a structure, architecture, or abstraction, run concrete scenarios against it. If real examples don't fit naturally, the structure needs revision.

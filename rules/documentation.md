# Documentation

## PRD Workflow

- **After planning mode** - Write the full plan as a PRD with filename `./.docs/PRD-[date]-[unique_name].md` (use `date -I` for the date).
- **After implementation** - Rename using `mv .docs/PRD-[date]-{,DONE-}[unique_name].md`, keeping the existing date and unique name.
- **PRD content** - Always include User Stories and Mermaid diagrams to guide the reader through the system flow.

## Tech Design Docs

- **Format** - Bullet points and descriptions of problems and solutions, not code blocks.
- **Purpose** - Final planning artifact before implementation begins. The programming language may still be unknown at this stage.

## README Requirements

README files must be simple, concise, and scannable:

- **Stack section** - One-line descriptions for each key library
- **Commands** - Just the essentials (dev, build, lint, type-check, validate)
- **Code Quality** - Bullet points covering linting, formatting, pre-commit, and commits
- **License** - Only include if explicitly requested. Clarify which license to use.

## Continuous Documentation

Critical processes must never exist only in someone's head:

- **Document all deployment processes** - How to deploy, rollback, and verify. If it's not written down, it doesn't exist.
- **Document generated file regeneration** - When a file is generated (OpenAPI specs, lock files, compiled types), document the command to regenerate it and when regeneration is needed.
- **Document environment setup** - New team members should be able to set up a working environment from documentation alone.
- **Update docs alongside code** - When changing behavior that's documented, update the documentation in the same PR.

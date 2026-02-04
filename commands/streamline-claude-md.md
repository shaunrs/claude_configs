# Streamline CLAUDE.md

Consolidate and streamline the project's CLAUDE.md file to be clear, concise, and scannable while preserving the intent of all rules.

## Process

### 1. Read and Analyze

Read the entire CLAUDE.md file. Identify:
- Repeated concepts explained in multiple places
- Verbose explanations that can be condensed
- Related rules scattered across different sections
- Code examples that are redundant or overly detailed

### 2. Define Categories

Group all content into logical categories. Common categories include:

- **Overview** - Domain, tech stack summary (one-liners)
- **Commands** - Essential CLI commands only
- **TypeScript/Types** - Type rules, inference, guards
- **Code Quality** - DRY, YAGNI, organization, naming, comments
- **Components/UI** - Architecture patterns, UI library rules
- **Testing** - Structure, rules, mocks, E2E patterns
- **Security** - Auth, encryption, env vars, sensitive data handling
- **Project Structure** - Routes, directories, workspaces
- **API/Backend** - Route handlers, validation, error handling
- **Git** - Commit conventions

Adapt categories to the specific project. Combine small sections; split large ones.

### 3. Apply Consolidation Rules

**Hierarchical bullet points with natural language:** Use bullet points as the primary formatting mechanism, with **inline bold** for key terms. Preserve complete sentences and explanatory context—don't reduce to telegraphic fragments.

```markdown
<!-- BEFORE: Verbose prose paragraphs -->
When you create new features, you should always ensure that DRY principles are followed.
This means extracting shared logic and avoiding duplication. You should never duplicate
validation schemas or mocks, as this creates maintenance burden. Additionally, YAGNI
should be applied to avoid building features that aren't needed yet, such as placeholders
or commented-out code "for later".

<!-- AFTER: Natural language in bullet structure -->
- Always adhere to **DRY** (Don't Repeat Yourself) principles and avoid code duplication. Validation schemas should always be shared from a single source of truth. Never duplicate zod (or similar) schemas or mock implementations.
- Follow **YAGNI** (You Aren't Gonna Need It) - Only build what is needed right now. Don't create placeholders, unused UI elements, or "for later" code. Three similar lines of code is better than a premature abstraction.
```

**Key principle:** Each bullet should be a complete thought with full sentences, not a fragment. Use bold for emphasis, but maintain natural reading flow.

**Use nested bullets for sub-rules with full context:**

```markdown
- **Prefer explicit dependencies over hidden ones** - Pass dependencies as parameters rather than reaching for globals or hardcoding instantiation. This enables easier testing and loose coupling.
  - Use DI frameworks when the codebase/framework convention calls for it (Angular, NestJS, Spring), but don't introduce DI containers just for their own sake.
  - In functional or simpler codebases, plain parameter passing achieves the same benefits without the overhead.
```

**Minimal code examples:** Keep only examples that demonstrate complex or non-obvious patterns. Remove examples for simple rules.

```markdown
<!-- REMOVE: Obvious rule needs no example -->
- Use descriptive variable names

<!-- KEEP: Pattern is non-obvious -->
- **Blocking providers** - Always render children (hidden):
  ```tsx
  <div style={{ display: ready ? "contents" : "none" }}>{children}</div>
  ```
```

**Avoid telegraphic style:** Don't reduce rules to fragments or lists of keywords. Each bullet should read naturally as a complete sentence or thought.

```markdown
<!-- BAD: Telegraphic fragments -->
- DRY - no duplication
- YAGNI - build what's needed
- Functions not classes

<!-- GOOD: Natural language with context -->
- Always adhere to DRY principles and avoid code duplication as far as practical.
- Follow YAGNI (You Aren't Gonna Need It) - Only build what is needed right now. Examples of premature optimization to avoid: [detailed list]
- Use standalone functions unless multiple functions share mutable module-level state with a lifecycle. Don't use classes just for grouping related functions.
```

**Single mention:** Each concept appears once. If DRY is mentioned, define it once with its full meaning, then reference by name only.

**Use `<example>` tags for demonstrating workflows:**

```markdown
<example>
user: Where are errors handled?
assistant: [Uses Grep to search for error handling patterns]
</example>
```

**NO tables** - Bullet points with inline bold are more scannable and consistent with Claude Code system prompts.

**Directory trees:** Use compact format with inline comments:

```markdown
<!-- BEFORE: Overly detailed tree -->
tests/
├── unit/
│   └── lib/
│       ├── schemas/
│       └── utils/
├── components/
├── e2e/
│   ├── pages/
│   └── fixtures/
└── mocks/

<!-- AFTER: Compact with inline comments -->
tests/
├── unit/           # Pure function tests (lib/utils, lib/schemas)
├── components/     # React component tests
├── e2e/            # Playwright (pages/, fixtures/)
└── mocks/          # Shared mocks (registered in setup.ts)
```

### 4. Preserve Intent and Natural Language

For each rule in the original:
1. Identify the underlying intent (what problem does it prevent?)
2. Express that intent in **clear, natural sentences** - not telegraphic fragments
3. Preserve explanatory context that clarifies the "why" behind the rule
4. Keep one clear example if the pattern is complex
5. Remove only: redundant repetition, verbose preambles, and unnecessary "benefits" sections

**Balance:** Condense structure (remove redundant sections, combine related rules), but **preserve natural language** (complete sentences, explanatory clauses, contextual details).

**Never lose a rule.** If a rule exists, there was a reason. Restructure and clarify it, don't reduce it to fragments.

### 5. Structure the Output

```markdown
# Project Name

**Key info:** One-liner (domain, critical notes)

## Tech Stack
One-liner list or compact format

## Commands
\`\`\`bash
# Only essential commands, no descriptions unless non-obvious
\`\`\`

## Category 1
### Subsection (if needed)
- **Key concept** - Explanation in imperative or declarative form
  - Sub-rule with more detail
  - Another related sub-rule
- **Another concept** - Brief, direct explanation

### Another Subsection
- Rules as hierarchical bullet points with inline bold
- Use nested bullets for sub-rules and clarifications
- Code examples only when pattern is non-obvious

## Category 2
...
```

**NO horizontal rules (`---`)** - They are not used in Claude Code system prompts. Section headers provide sufficient visual separation.

### 6. Validate

After streamlining:
1. Compare section-by-section with original to ensure no rules were lost
2. Verify all critical warnings (security, breaking changes) are preserved
3. Check that code examples still compile/make sense in isolation
4. Confirm the reduction is at least 40-60% in line count

## Output

Write the streamlined version to `CLAUDE.streamlined.md` in the same directory. Report the line count reduction. Ask the user if they want to replace the original.

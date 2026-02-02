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

| Category | Typical Content |
|----------|-----------------|
| Overview | Domain, tech stack summary (one-liners) |
| Commands | Essential CLI commands only |
| TypeScript | Type rules, inference, guards |
| Code Quality | DRY, YAGNI, organization, naming, comments |
| Components/UI | Architecture patterns, UI library rules |
| Testing | Structure, rules, mocks, E2E patterns |
| Security | Auth, encryption, env vars, sensitive data handling |
| Project Structure | Routes, directories, workspaces |
| API/Backend | Route handlers, validation, error handling |
| Git | Commit conventions |

Adapt categories to the specific project. Combine small sections; split large ones.

### 3. Apply Consolidation Rules

**Tables over prose:** Convert lists of related rules into scannable tables.

```markdown
<!-- BEFORE: Verbose -->
- Use DRY principles to extract shared logic
- Apply YAGNI to avoid building unused features
- Follow single responsibility for files

<!-- AFTER: Table -->
| Principle | Rule |
|-----------|------|
| **DRY** | Extract shared logic. Never duplicate validation or mocks. |
| **YAGNI** | Only build what's needed now. No placeholders or "for later" code. |
| **Single Responsibility** | One clear purpose per file. |
```

**Minimal code examples:** Keep only examples that demonstrate complex or non-obvious patterns. Remove examples for simple rules.

```markdown
<!-- REMOVE: Obvious rule needs no example -->
- Use descriptive variable names

<!-- KEEP: Pattern is non-obvious -->
**Blocking providers** - Always render children (hidden):
\`\`\`tsx
<div style={{ display: ready ? "contents" : "none" }}>{children}</div>
\`\`\`
```

**Single mention:** Each concept appears once. If DRY is mentioned, define it once with its full meaning, then reference by name only.

**Inline over nested:** Prefer `**bold** inline` definitions over nested bullet points.

**Directory trees:** Use compact format, one line per significant path:

```markdown
<!-- BEFORE -->
tests/
├── unit/
│   └── lib/
│       ├── schemas/
│       └── utils/

<!-- AFTER -->
tests/
├── unit/           # Pure function tests (lib/utils, lib/schemas)
├── components/     # React component tests
├── e2e/            # Playwright
└── mocks/          # Shared mocks (registered in setup.ts)
```

### 4. Preserve Intent

For each rule in the original:
1. Identify the underlying intent (what problem does it prevent?)
2. Express that intent in the fewest words possible
3. Keep one clear example if the pattern is complex
4. Remove redundant explanations, "benefits" sections, and obvious justifications

**Never lose a rule.** If a rule exists, there was a reason. Condense it, don't delete it.

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

---

## Category 1
### Subsection (if needed)
Rules as tables or tight bullet points

---

## Category 2
...
```

Use `---` horizontal rules to separate major sections for visual scanning.

### 6. Validate

After streamlining:
1. Compare section-by-section with original to ensure no rules were lost
2. Verify all critical warnings (security, breaking changes) are preserved
3. Check that code examples still compile/make sense in isolation
4. Confirm the reduction is at least 40-60% in line count

## Output

Write the streamlined version to `CLAUDE.streamlined.md` in the same directory. Report the line count reduction. Ask the user if they want to replace the original.
